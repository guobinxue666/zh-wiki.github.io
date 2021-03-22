---
title: 高通 Camx Bring up
toc: true
date: 2020-01-01 00:00:30
tags: 高通CamxBring Up Sensor
---

# 一、Sensor Bring Up
------

## 1.1 HAL层的配置

1. 移植驱动代码到相应的路径

   vendor/qcom/proprietary/chi-cdk/oem/qcom/sensor/

   驱动文件名字根据项目而定

   <img src="%E9%AB%98%E9%80%9A%20Camx%20Bring%20up/image-20201111142550122.png" alt="驱动代码" style="zoom:200%;" />

2. sensor xml 相关配置

   <details>
   <summary>lime_sunny_hi259_macro_sensor.xml</summary>

   ```xml
    <slaveInfo>
       <!--Name of the sensor -->
       <sensorName>lime_sunny_hi259_macro</sensorName>
       <!--8-bit or 10-bit write slave address
           For External Sensors for which camx needs not probe the slave address shoule be as 0 -->
       <slaveAddress>0x60</slaveAddress>
       <!--Register address / data size in bytes -->
       <regAddrType range="[1,4]">2</regAddrType>
       <!--Register address / data size in bytes -->
       <regDataType range="[1,4]">2</regDataType>
       <!--Register address for sensor Id -->
       <sensorIdRegAddr>0x04</sensorIdRegAddr>
       <!--Sensor Id 0xE1-->
       <sensorId>0x113</sensorId>
       <!--Mask for sensor id. Sensor Id may only be few bits -->
       <sensorIdMask>4294967295</sensorIdMask>
       <!--I2C frequency mode of slave
           Supported modes are: STANDARD (100 KHz), FAST (400 KHz), FAST_PLUS (1 MHz), CUSTOM (Custom frequency in DTSI) -->
       <i2cFrequencyMode>FAST</i2cFrequencyMode>
       <!--Sequence of power configuration type and configuration value required to control power to the device -->
   
      <powerUpSequence>
         <powerSetting>
           <configType>RESET</configType>
           <configValue>1</configValue>
           <delayMs>1</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>CUSTOM_GPIO1</configType>
           <configValue>1</configValue>
           <delayMs>0</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>CUSTOM_GPIO2</configType>
           <configValue>1</configValue>
           <delayMs>2</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>MCLK</configType>
           <configValue>24000000</configValue>
           <delayMs>8</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>RESET</configType>
           <configValue>0</configValue>
           <delayMs>1</delayMs>
         </powerSetting>
       </powerUpSequence>
       <!--Sequence of power configuration type and configuration value required to control power to the device -->
       <powerDownSequence>
         <!--Power setting configuration
             Contains: configType, configValue and delay in milli seconds -->
         <powerSetting>
   		  <configType>RESET</configType>
           <configValue>1</configValue>
           <delayMs>1</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>MCLK</configType>
           <configValue>0</configValue>
           <delayMs>0</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>CUSTOM_GPIO1</configType>
           <configValue>0</configValue>
           <delayMs>0</delayMs>
         </powerSetting>
         <powerSetting>
           <configType>CUSTOM_GPIO2</configType>
           <configValue>0</configValue>
           <delayMs>1</delayMs>
         </powerSetting>
       </powerDownSequence>
     </slaveInfo>
   ```

   </details>

   - sensor name 跟sensor  文件夹名字一致
   - slaveAddress IIC 从机地址
   - sensor id 寄存器地址 以及 sensor id
   - 上下电时序 (为了保险首先将reset引脚设置为禁止状态)

3. module xml 配置

   <details>
   <summary>lime_sunny_hi259_macro_module.xml</summary>

   ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <!--========================================================================-->
   <!-- Copyright (c) 2018 Qualcomm Technologies, Inc.                         -->
   <!-- All Rights Reserved.                                                   -->
   <!-- Confidential and Proprietary - Qualcomm Technologies, Inc.             -->
   <!--========================================================================-->
   <cameraModuleData
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:noNamespaceSchemaLocation="..\..\..\api\sensor\camxmoduleconfig.xsd">
     <module_version major_revision="1" minor_revision="0" incr_revision="0"/>
   
       <!--Module group can contain either 1 module or 2 modules
         Dual camera, stereo camera use cases contain 2 modules in the group -->
     <moduleGroup>
       <!--Module configuration -->
       <moduleConfiguration description="Module configuration">
         <!--CameraId is the id to which DTSI node is mapped.
             Typically CameraId is the slot Id for non combo mode. -->
         <cameraId>3</cameraId>
         <!--Name of the module integrator -->
         <moduleName>sunny</moduleName>
         <!--Name of the sensor in the image sensor module -->
         <sensorName>lime_sunny_hi259_macro</sensorName>
         <!--Actuator name in the image sensor module
             This is an optional element. Skip this element if actuator is not present -->
         <actuatorName></actuatorName>
         <oisName></oisName>
         <!--EEPROM name in the image sensor module
             This is an optional element. Skip this element if EEPROM is not present -->
         <eepromName></eepromName>
         <!--Flash name is used to used to open binary.
             Binary name is of form flashName_flash.bin Ex:- pmic_flash.bin -->
         <flashName></flashName>
         <!--Chromatix name is used to used to open binary.
             Binary name is of the form sensor_model_chromatix.bin -->
         <chromatixName>lime_sunny_hi259_macro</chromatixName>
         <!--Position of the sensor module.
             Valid values are: REAR, FRONT, REAR_AUX, FRONT_AUX, EXTERNAL -->
         <position>REAR_AUX</position>
         <!--CSI Information -->
         <CSIInfo description="CSI Information">
             <laneAssign>2</laneAssign>
             <isComboMode>1</isComboMode>
         </CSIInfo>
         <!--Lens information -->
         <lensInfo description="Lens Information">
           <!--Focal length of the lens in millimeters. -->
           <focalLength>4.71</focalLength>
           <!--F-Number of the optical system. -->
           <fNumber>1.79</fNumber>
           <!--Minimum focus distance in meters. -->
           <minFocusDistance>0.1</minFocusDistance>
           <!--Total focus distance in meters. -->
           <maxFocusDistance>1.9</maxFocusDistance>
           <!--Horizontal view angle in degrees. -->
           <horizontalViewAngle>67</horizontalViewAngle>
           <!--Vertical view angle in degrees. -->
           <verticalViewAngle>53</verticalViewAngle>
           <!--Maximum Roll Degree. Valid values are: 0, 90, 180, 270, 360 -->
           <maxRollDegree>270</maxRollDegree>
           <!--Maximum Pitch Degree. Valid values are: 0 to 359 -->
           <maxPitchDegree>360</maxPitchDegree>
           <!--Maximum Yaw Degree. Valid values are: 0 to 359 -->
           <maxYawDegree>360</maxYawDegree>
         </lensInfo>
         <pdafName></pdafName>
       </moduleConfiguration>
     </moduleGroup>
   </cameraModuleData>
   ```

   </details>

   - 配置 CameraId 与kernel dts 相对应
   - 配置sensorname 与 sensor xml 保持一致
   - 配置 chromatixName 与 sensor name 保持一致
   - 配置 position 摄像头位置 前摄 后摄 或者 后辅
   - 配置 CSIInfo mipi 通道

# 二、Flash Bring Up

------

## 2.1 kernel 端dtsi配置

1. 在对应的sensor节点下应用flash的节点

   ```c
   qcom,cam-sensor0 {
       ......
       led-flash-src = <&led_flash_rear>;
       ......
   }
   ```

2. flash 的节点一般定义在 sensor节点的同文件中。

   flash 工作方式有2种：flash(闪光灯)，torch(手电筒)。

   这里主要调用了pmic的节点，主要是flash电流的一下配置，一般不需要修改。

   ```c
   led_flash_rear: qcom,camera-flash@0 {
       cell-index = <0>;
       compatible = "qcom,camera-flash";
       flash-source  = <&pmi632_flash0 &pmi632_flash1>;
       torch-source  = <&pmi632_torch0 &pmi632_torch1>;
       switch-source = <&pmi632_switch0 &pmi632_switch0>;
       status = "ok";
   };
   ```

   ```c
   pmi632_flash0: qcom,flash_0 {
       label = "flash";
       qcom,led-name = "led:flash_0";
       qcom,max-current = <1500>;
       qcom,default-led-trigger = "flash0_trigger";
       qcom,id = <0>;
       qcom,current-ma = <1200>;
       qcom,duration-ms = <1280>;
       qcom,ires-ua = <12500>;
       qcom,hdrm-voltage-mv = <400>;
       qcom,hdrm-vol-hi-lo-win-mv = <100>;
   };
   ```

## 2.2 vendor 端配置

1. 配置flash XML

   ```xml
   //chi-cdk/oem/qcom/flash/back_sensor_flash.xml
   <flashName>back_pmic_flash</flashName>
   <flashDriverType>PMIC</flashDriverType>
   <numberOfFlashs range="[1,3]">1</numberOfFlashs>
   ```

2. 配置module XML 

   ```xml
   //chi-cdk/oem/qcom/module/xxx.xml
   <flashName>back_pmic_flash</flashName>
   ```

3. 配置编译路径（在对应的项目yaml中添加以下内容）

   ```yaml
   //chi-cdk/tools/buildbins/xxx.yaml
   sensor_drivers:
       - com.qti.sensormodule.lime_sunny_s5kgm1st_main:
       - sensor/lime_sunny_s5kgm1st_main/lime_sunny_s5kgm1st_main_sensor.xml
       - module/lime_sunny_s5kgm1st_main_module.xml
       - flash/back_sensor_flash.xml 
   ```

# 三、AF Bring UP

------

## 3.1 kernel 端dtsi配置

1. 通过原理图查看供电方式

   通过查看原理图可以得出，该AF是通过使能LOD 的EN 引脚来供电的。（GPIO控制）

   ![供电方式](%E9%AB%98%E9%80%9A%20Camx%20Bring%20up/image-20210319144520798.png)

2. 找到对应sensor节点调用的AF节点

   ```c
   qcom,cam-sensor0 {
       ......
       actuator-src = <&actuator_rear>;
       ......
   }
   ```

3. 找到调用的AF节点，配置供电方式

   通过看下面的代码，平台默认给的是PMIC的供电方式，我们用的是GPIO控制。

   ```c
   actuator_rear: qcom,actuator0 {
       cell-index = <0>;
       compatible = "qcom,actuator";
       cci-master = <0>;
       // 我个人理解这一块是 pmic 供电配置 注释掉 start
       //cam_vaf-supply = <&L5P>;
       //regulator-names = "cam_vaf";
       //rgltr-cntrl-support;
       //rgltr-min-voltage = <2800000>;
       //rgltr-max-voltage = <2800000>;
       //rgltr-load-current = <100000>;
       // 我个人理解这一块是 pmic 供电配置 注释掉 end
       pinctrl-0 = <&cam_sensor_rear0_vaf_active>;   //调用了pinctrl 中的节点，没有的话自己定义
       pinctrl-1 = <&cam_sensor_rear0_vaf_suspend>;  //调用了pinctrl 中的节点，没有的话自己定义
       gpios = <&tlmm 83 0>;
       gpio-vaf = <0>;
       gpio-req-tbl-num = <0>;
       gpio-req-tbl-flags = <0>;
       gpio-req-tbl-label = "CAM_VAF";
       status = "ok";
   };
   ```

   如果说按照上面的配置，kernel 端dtis就已经配置完成了。如果说没有将pmic的供电方式注释掉，可能还要在sensor的节点中，加入以下配置。

   ```c
   qcom,cam-sensor0 {
       ......
       rgltr-min-voltage = < 0 >;
       rgltr-max-voltage = < 0 >;
       rgltr-load-current = < 0 >;
       ......
   }
   ```

4. 根据dtsi配置我们知道平台默认给的是pmic供电方式，我们现在将AF供电方式配置为GPIO供电LDO。所以说kernel 中上电代码也要修改一下

   这里需要说明的是，在上电过程中，如果上电失败会有 `goto power_up_failed`,这个里面也会对gpio 和 pmic 进行操作。所以改一下。改的内容都一样。该注释注释，该新增新增。另外，上电修改ok, 下电也要修改。以下是上电的修改范例。

   ```c
   int cam_sensor_core_power_up(struct cam_sensor_power_ctrl_t *ctrl, struct cam_hw_soc_info *soc_info)
   {
       ......
        //通过这一块可以看出是gpio的操作，所以所在该case中新增 SENSOR_VAF
       case SENSOR_RESET:
       case SENSOR_STANDBY:
       case SENSOR_CUSTOM_GPIO1:
       case SENSOR_CUSTOM_GPIO2:
       case SENSOR_VAF:
           rc = msm_cam_sensor_handle_reg_gpio(......);
       
       ......
       //通过这一块可以是pmic控制器的操作，既然我们用gpio的方式，所以要注释掉
       case SENSOR_VANA:
       case SENSOR_VDIG:
       case SENSOR_VIO:
       //case SENSOR_VAF:
       case SENSOR_VAF_PWDM:
       case SENSOR_CUSTOM_REG1:
       case SENSOR_CUSTOM_REG2:
       rc =  cam_soc_util_regulator_enable(......);
   }
   ```

   

## 3.2 vendor 端配置

1. 将AF驱动文件放入指定路径，配置参数

   ```xml
   //chi-cdk/oem/qcom/actuator/xxx.xml
   <actuatorName>lime_sunny_dw9800_s5kgm1st</actuatorName>
   <slaveAddress>0x18</slaveAddress>
   <actuatorType>VCM</actuatorType>
     
       <powerUpSequence>
         <powerSetting>
           <!--Power configuration type Supported types are: MCLK, VANA, VDIG, VIO, VAF, RESET, STANDBY -->
           <configType>VAF</configType>
           <!--Configuration value for the type of configuration -->
           <configValue>1</configValue>
           <!--Delay in milli seconds -->
           <delayMs>1</delayMs>
         </powerSetting>
       </powerUpSequence>
   
       <powerDownSequence>
         <powerSetting>
           <!--Power configuration type Supported types are: MCLK, VANA, VDIG, VIO, VAF, RESET, STANDBY -->
           <configType>VAF</configType>
           <!--Configuration value for the type of configuration -->
           <configValue>0</configValue>
           <!--Delay in milli seconds -->
           <delayMs>1</delayMs>
         </powerSetting>
       </powerDownSequence>
   ```

2. 配置module XML 

   ```xml
   //chi-cdk/oem/qcom/module/xxx.xml
   <actuatorName>lime_sunny_dw9800_s5kgm1st</actuatorName>
   ```

3. 配置编译路径（在对应的项目yaml中添加以下内容）

   ```yaml
   //chi-cdk/tools/buildbins/xxx.yaml
   sensor_drivers:
       - com.qti.sensormodule.lime_sunny_s5kgm1st_main:
       - sensor/lime_sunny_s5kgm1st_main/lime_sunny_s5kgm1st_main_sensor.xml
       - module/lime_sunny_s5kgm1st_main_module.xml
       - flash/back_sensor_flash.xml 
       - actuator/lime_sunny_dw9800_s5kgm1st_actuator.xml
   ```

# 四、PDAF Bring Up

------

## 4.1 Vendor 端配置

pdaf 只需要配置 vendor即可。

1. 将pdaf XML 导入到相应的路径

   chi-cdk/oem/qcom/sensor/lime_sunny_s5kgm1st_main/xxx_pdaf.xml

2. 配置module XML 

   ```xml
   //chi-cdk/oem/qcom/module/xxx.xml
   <pdafName>lime_ofilm_s5kgm1st_main_pdaf</pdafName>
   ```

3. 配置编译路径（在对应的项目yaml中添加以下内容）

   ```yaml
   //chi-cdk/tools/buildbins/xxx.yaml
   sensor_drivers:
       - com.qti.sensormodule.lime_sunny_s5kgm1st_main:
       - sensor/lime_sunny_s5kgm1st_main/lime_sunny_s5kgm1st_main_sensor.xml
       - module/lime_sunny_s5kgm1st_main_module.xml
       - flash/back_sensor_flash.xml 
       - actuator/lime_sunny_dw9800_s5kgm1st_actuator.xml
       - sensor/lime_sunny_s5kgm1st_main/lime_sunny_s5kgm1st_main_pdaf.xml
   ```

# 五、 OPT Bring UP

------

## 5.1 Dump EEprom Data

```bash
adb shell "echo dumpSensorEEPROMData=1 >> /vendor/etc/camera/camxoverridesettings.txt"
```

数据存放位置： **/data/vendor/camera/xxx_kbuffer_OTP.txt**

# 六、双摄帧同步导通

------

1. 将camx平台默认的几个属性开启

   ```xml
    //camxsettings.xml      
   <VariableName>multiCameraEnable</VariableName>
   <VariableName>multiCameraHWSyncMask</VariableName>
   <VariableName>multiCameraFrameSyncMask</VariableName>
   <VariableName>multiCameraFPSMatchMask</VariableName>
   //高通的case还给了这两个，j19S项目代码中没有找到这连个配置，没有配置也是OK的
   adb shell "echo multiCamera3ASync=QTI >> /vendor/etc/camera/camxoverridesettings.txt"
   adb shell "echo multiCameraSATEnable=1 >> /vendor/etc/camera/camxoverridesettings.txt"
   ```

2. 在Camera Id 映射的位置指定双摄的Camera Id

   ```c++
   static LogicalCameraConfiguration logicalCameraConfigurationKamorta[] =
   {
       /*cameraId cameraType              exposeFlag phyDevCnt  sensorId, transition low, high, smoothZoom, alwaysOn  realtimeEngine            primarySensorID, hwMaster*/
       {0,        LogicalCameraType_Default, TRUE,      1,    {{0,                    0.0, 0.0,   FALSE,    TRUE,     RealtimeEngineType_IFE}},  0,              0    }, ///< Wide camera
       {1,        LogicalCameraType_Default, TRUE,      1,    {{2,                    0.0, 0.0,   FALSE,    TRUE,     RealtimeEngineType_IFE}},  2,              2    }, ///< Front camera
       {2,        LogicalCameraType_Default, TRUE,      1,    {{1,                    0.0, 0.0,   FALSE,    TRUE,     RealtimeEngineType_IFE}},  1,              1    }, ///< Tele camera
       {3,        LogicalCameraType_Default, TRUE,      1,    {{3,                    0.0, 0.0,   FALSE,    TRUE,     RealtimeEngineType_IFE}},  3,              3    },
       {4,        LogicalCameraType_RTB,     TRUE,      2,    {{0,                    2.0, 8.0,   FALSE,    TRUE,     RealtimeEngineType_IFE},
                                                               {2,                    1.0, 2.0,   FALSE,    TRUE,     RealtimeEngineType_IFE}},  0,              0    }, ///< RTB
   };
   ```

   在j19S项目中更改了一下参数 。（双摄预览出图是辐摄）

   - 第一个参数: 调节zoom值
   - 第二个参数: 配置主摄的camera Id

   ![双摄帧同步](%E9%AB%98%E9%80%9A%20Camx%20Bring%20up/image-20201112172851742.png)

   在j19S项目中还有存在一个平台bug （上面介绍的结构体双摄不能为最后一个成员）

   高通给的patch

   ```c++
   //vendor/qcom/proprietary/chi-cdk / oem/qcom/feature2/chifeature2graphselector/chifeature2graphselector.cpp
   VOID ChiFeature2GraphSelector::BuildCameraIdSet()
   {
       //Add by junwei.zhou according Qcom case num 04763737
       //fix platform design
   #ifdef __XIAOMI_CAMERA__
       m_cameraIdMap.insert({ { cameraIdSetSingle },  SINGLE_CAMERA });
       m_cameraIdMap.insert({ { cameraIdSetBokeh },   BOKEH_CAMERA });
       m_cameraIdMap.insert({ { cameraIdSetMulti },   MULTI_CAMERA });
       m_cameraIdMap.insert({ { cameraIdSetFusion },  FUSION_CAMERA });
   #else
       m_cameraIdMap.insert({ { cameraIdSetSingle },  SINGLE_CAMERA });
       m_cameraIdMap.insert({ { cameraIdSetMulti },   MULTI_CAMERA });
       m_cameraIdMap.insert({ { cameraIdSetBokeh },   BOKEH_CAMERA });
       m_cameraIdMap.insert({ { cameraIdSetFusion },  FUSION_CAMERA });
   #endif
   }
   ```

3. 分别配置主摄和辅摄的setting

   - 主摄：masterSettings

   - 辐摄：slaveSettings

