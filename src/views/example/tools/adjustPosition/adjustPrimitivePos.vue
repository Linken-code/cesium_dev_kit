<template>
  <div class="sky-box">
    <div id="cesiumContainer"
         class="map3d-contaner"></div>

    <div class="cust-gui-box"
         id="cust-gui-box"></div>
    <div v-cust-drag-dialog>
      <el-dialog v-model="dialogDirectiveVisible"
                 title="获取模型矩阵"
                 width="30%"
                 :close-on-click-modal="false">
        <p>【modelTransformMatrix】{{ modelTransformMatrix }}</p>
        <p>【modelMatrix】{{ modelMatrix }}</p>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogDirectiveVisible = false">取消</el-button>
            <el-button type="primary"
                       @click="dialogDirectiveVisible = false">
              确定
            </el-button>
          </span>
        </template>
      </el-dialog>
    </div>
  </div>
</template>
<script >
import * as Cesium from 'cesium'
import { initCesium } from '@/utils/cesiumPluginsExtends/index'
export default {
  data() {
    return {
      dialogDirectiveVisible: false,
      modelMatrix: '',
      modelTransformMatrix: ''
    }
  },
  mounted() {
    this.initMap()
  },
  methods: {
    initMap() {
      const tempData = [
        {
          id: 3,
          name: '高德地图02',
          type: 'UrlTemplateImageryProvider',
          classConfig: {
            url: 'https://webst02.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}',
          },
          interfaceConfig: {},
          offset: '0,0',
          invertswitch: 0,
          filterRGB: '#ffffff',
          showswitch: 1,
          weigh: 13,
          createtime: 1624346908,
          updatetime: 1647395260,
        }]
      const {
        viewer,
        control,
      } = new initCesium(
        {
          cesiumGlobal: Cesium,
          containerId: 'cesiumContainer',
          viewerConfig: {
            infoBox: false,
            shouldAnimate: true,
          },
          extraConfig: {},
          MapImageryList: tempData
        })


      this.c_viewer = viewer;

      this.control = control;
      this.control.setDefSceneConfig()
      this.control.setBloomLightScene()
      let tileset = this.c_viewer.scene.primitives.add(
        new Cesium.Cesium3DTileset({
          url: 'static/data/3DTiles/building/tileset.json',
        }),
      )
      tileset.style = new Cesium.Cesium3DTileStyle({
        color: {
          conditions: [
            ['${height} >= 300', 'rgba(0, 149, 251, 0.3)'],
            ['${height} >= 200', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 100', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 50', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 25', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 10', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 5', 'rgb(0, 149, 251, 0.3)'],
            ['true', 'rgb(0, 149, 251, 0.3)'],
          ],
        },
      })
      this.c_viewer.flyTo(tileset);
      this.control.showPrimitiveMatrixPanel({
        elementId: 'cust-gui-box', primitives: tileset, cb: resModelMatrix => {
          console.log(resModelMatrix)
          this.modelTransformMatrix = JSON.stringify(resModelMatrix.modelTransformMatrix);
          this.modelMatrix = resModelMatrix.modelMatrix;
          this.dialogDirectiveVisible = true;
        }
      });
    }
  },
  beforeUnmount() {
    this.c_viewer = null;
    this.control = null;
  }
}
</script>
<style lang="scss" scoped>
.sky-box {
  position: relative;
}
.map3d-contaner {
  width: 100%;
  height: 100%;
  overflow: hidden;
  .ctrl-group {
    position: absolute;
    top: 10px;
    right: 20px;
    z-index: 999;
    .reset-home-btn {
      color: #36a3f7;
      cursor: pointer;
    }
  }
}
.cust-gui-box {
  position: absolute;
  right: 0px;
  top: 0px;
}
</style>
