<template>

<div class="control-section">
    <div style="width:100%">
        <ejs-diagram style='display:block' id="diagram" :width='width' :height='height' :getConnectorDefaults='getConnectorDefaults' :snapSettings='snapSettings' :layout='layout' :dataSourceSettings='dataSourceSettings' :tool='tool' :setNodeTemplate='setNodeTemplate'></ejs-diagram>
    </div>
<div id="action-description">
    <p>
        This sample visualizes a project development process using Program Evaluation Review Technique (PERT). Complex hierarchical tree layout algorithm is used to automatically arrange the nodes.
    </p>
</div>
<div id="description">
    <p>
        This example shows how to generate a PERT chart from an external data source. The <code>dataSourceSettings</code> property can be used to map an external data source with the diagram control. 
        The <code>layout</code> property can be used to automatically position the nodes. In this example, the nodes are arranged from left to right of the diagram. The <code>orientation</code> property can be used to define the orientation of the layouts.
    </p>

    <p style="font-weight: 500">Injecting Module</p>
    <p>
        The diagram component’s features are segregated into individual feature-wise modules. To generate diagrams from an external
        data source, inject <code>DataBinding</code> module using <code>provide: { diagram: [DataBinding] }</code> method. To automatically arrange the objects in a PERT Chart, inject
        <code>ComplexHierarchicalTree</code> module using <code>provide: { diagram: [ComplexHierarchicalTree] }</code> method.
    </p>
    <br>
</div>
</div>

</template>
<style>
</style>
<script lang="ts">
import Vue from "vue";
import {
  SnapConstraints,
  TextStyleModel,
  TextElement,
  HorizontalAlignment,
  DiagramTools
} from "@syncfusion/ej2-vue-diagrams";
import {
  HierarchicalTree,
  ComplexHierarchicalTree,
  ConnectorModel
} from "@syncfusion/ej2-vue-diagrams";
import {
  DiagramPlugin,
  Diagram,
  NodeModel,
  DataBinding,
  DiagramElement,
  StackPanel,
  VerticalAlignment
} from "@syncfusion/ej2-vue-diagrams";

import { pertChartData } from "./diagram-data";

import { DataManager } from "@syncfusion/ej2-data";

Vue.use(DiagramPlugin);

let diagramInstance: Diagram;

function getTextElement(
  text: string,
  alignment: HorizontalAlignment,
  width?: number | any,
  valignment?: VerticalAlignment | any
): DiagramElement {
  let textElement: TextElement = new TextElement();
  textElement.content = text;
  textElement.width = width;
  textElement.height = 25;
  textElement.horizontalAlignment = alignment;
  textElement.verticalAlignment = valignment;
  textElement.style.strokeWidth = 1;
  textElement.style.strokeColor = "#b5b5b5";
  textElement.style.fill = "transparent";
  textElement.style.color = "#3c3c3c";
  textElement.relativeMode = "Object";
  return textElement;
}
let sDate: string = "startDate";
let eDate: string = "endDate";
let duration: string = "duration";
let addRows: Function = (column: StackPanel, node: NodeModel) => {
  column.children.push(
    getTextElement((node.data as DataInfo)[sDate], "Left", 70)
  );
  column.children.push(
    getTextElement((node.data as DataInfo)[duration], "Center", 30)
  );
  column.children.push(
    getTextElement((node.data as DataInfo)[eDate], "Right", 70)
  );
};

export default Vue.extend({
  data: function() {
    return {
      width: "100%",
      height: "499px",
      snapSettings: { constraints: SnapConstraints.None },
      dataSourceSettings: {
        id: "id",
        parentId: "Category",
        dataManager: new DataManager(pertChartData as JSON[]),
        //binds the external data with node
        doBinding: (nodeModel: NodeModel, data: object, diagram: Diagram) => {
          let shape: string = "shape";
          let name: string = "id";
          /* tslint:disable:no-string-literal */
          nodeModel["shape"] = { type: "Text" };
        }
      },
      layout: {
        type: "ComplexHierarchicalTree",
        orientation: "LeftToRight",
        verticalSpacing: 100,
        horizontalSpacing: 70
      },
      //Sets the default values of connector
      getConnectorDefaults: (connector: ConnectorModel, diagram: Diagram) => {
        connector.type = "Straight";
        if (connector.style) connector.style.strokeColor = "#979797";
        if (connector.targetDecorator) {
          connector.targetDecorator.width = 10;
          connector.targetDecorator.height = 10;
          connector.targetDecorator.style = {
            fill: "#979797",
            strokeColor: "#979797"
          };
        }
        return connector;
      },
      //used to customize template of the node.
      setNodeTemplate: (node: NodeModel) => {
        return getNodeTemplate(node);
      },
      tool: DiagramTools.ZoomPan
    };
  },
  provide: {
    diagram: [DataBinding, HierarchicalTree, ComplexHierarchicalTree]
  },
  mounted: function() {
    let obj: any = document.getElementById("diagram");
    diagramInstance = obj.ej2_instances[0];
    diagramInstance.fitToPage();
  }
});

//customization of the node template.
function getNodeTemplate(node: NodeModel): StackPanel {
  let table: StackPanel = new StackPanel();
  table.style.fill = "#0069d9";
  table.orientation = "Vertical";
  let nameKey: string = "id";
  let stack: StackPanel = new StackPanel();
  stack.children = [];
  stack.height = 25;
  stack.orientation = "Horizontal";
  stack.style.fill = "white";
  stack.horizontalAlignment = "Stretch";
  addRows(stack, node);
  table.children = [
    getTextElement((node.data as DataInfo)[nameKey], "Stretch", 170, "Stretch"),
    stack
  ];
  (table.children[0].style as TextStyleModel).color = "white";
  (table.children[0].style as TextStyleModel).fontSize = 14;
  return table;
}

export interface DataInfo {
  [key: string]: string;
}
</script>