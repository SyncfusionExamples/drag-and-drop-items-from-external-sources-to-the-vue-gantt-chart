<template>
  <div style="width: 75%; float: left;">
    <ejs-gantt ref="gantt" :dataSource="data"
             :taskFields="taskFields"
             :treeColumnIndex="1"
             :editSettings="editOptions"
             :resources="resources"
             :resourceFields="resourceFields"
             :labelSettings="labelOptions"
             :height="450">
      <e-columns>
        <e-column field="TaskID" headerText="Task ID" width="120" textAlign="Right"></e-column>
        <e-column field="TaskName" headerText="Task Name" textAlign="Left" width="200"></e-column>
        <e-column field="resources" width="200"></e-column>
        <e-column field="StartDate" headerText="Start Date" textAlign="Right" format="dd/MM/yyyy" width="120"></e-column>
        <e-column field="Duration" headerText="Duration" textAlign="Right" width="120"></e-column>
      </e-columns>
    </ejs-gantt>
  </div>
  <ejs-treeview :fields="treeViewData" :allowDragAndDrop="true" :nodeDragStop="onDragStop"></ejs-treeview>
</template>

<script>
import {GanttComponent, ColumnsDirective, ColumnDirective, Edit, Selection} from '@syncfusion/ej2-vue-gantt'
import {TreeViewComponent} from '@syncfusion/ej2-vue-navigations'
import {closest} from '@syncfusion/ej2-base'
import {resourceData, editingResources} from './data.js'
export default{
  name: 'App',
  components: {
    'ejs-gantt': GanttComponent,
    'e-columns': ColumnsDirective,
    'e-column': ColumnDirective,
    'ejs-treeview': TreeViewComponent
  },
  provide:{
    gantt: [Edit, Selection]
  },
  methods:{
    onDragStop(args){
      args.cancel = true
      var ganttObj = this.$refs.gantt.ej2Instances;
      var chartRowEle = closest(args.target, '.e-chart-row');
      var gridRowEle = closest(args.target, '.e-row');
      let selectedData;
      let resources = [];
      let record;
      if(chartRowEle || gridRowEle){
        record = args.draggedNodeData;
        let index = chartRowEle ? parseInt(chartRowEle.ariaRowIndex) : ganttObj.treeGrid.getRows().indexOf(gridRowEle);
        ganttObj.selectRow(index);
        selectedData = ganttObj.flatData[ganttObj.selectedRowIndex].taskData;
        if(selectedData.resources){
          for(let i=0; i<selectedData.resources.length; i++){
            resources.push(selectedData.resources[i].resourceId);
          }
        }
        resources.push(parseInt(record.id));
        ganttObj.updateRecordByID({TaskID: selectedData.TaskID, resources: resources});
      }
    }
  },
  data(){
    return{
      data: resourceData,
      editOptions:{
        allowEditing: true,
      },
      treeViewData: { dataSource: editingResources, id: 'resourceId', text: 'resourceName'},
      labelOptions:{
        rightLabel: 'resources',
      },
      resources:editingResources,
      resourceFields:{
        id: 'resourceId',
        name: 'resourceName'
      },
      taskFields:{
        id: 'TaskID',
        name: 'TaskName',
        resourceInfo:'resources',
        startDate: 'StartDate',
        endDate: 'EndDate',
        duration: 'Duration',
        progress: 'Progress',
        dependency: 'Predecessor',
        child: 'subtasks'
      },
      
    }
  }
}

</script>

<style>
  @import url("https://cdn.syncfusion.com/ej2/material.css");
</style>

