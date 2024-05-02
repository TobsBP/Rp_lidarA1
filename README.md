# Rp_lidarA1

https://automaticaddison.com/navigation-and-slam-using-the-ros-2-navigation-stack/
-------------------------------------------------------------
Distância minima/maxima rplidar A1 = 0.15 - 15m

Requer colcon instalado;

1º Inicia o ambiente: source /opt/ros/humble/setup.bash
2º Abre o programa: rviz2

Para não precisar ficar rodando 'source /opt/ros/humble/setup.bash' 
digite gedit ~/.bashrc para abrir meio que um arquivo com varias informações
vá para ultima linha de enter e cole isso: source /opt/ros/humble/setup.bash
salve e saia, pronto.

--------------------------------------------------------------
export XAUTH=/tmp/.docker.xauth
touch $XAUTH
xauth nlist $DISPLAY | sed -e LD_LIBRARY_PATH=/opt/ros/noetic/lib:/opt/ros/noetic/lib/aarch64-linux-gnu:/usr/lib/aarch64-linux-gnu/tegra | xauth -f $XAUTH nmerge -
--------------------------------------------------------------
Criando um packge

cd ros2_ws/src
ros2 pkg create mapping --build-type ament_python --dependencies rclpy

--------------------------------------------------------------
Passo a passo para rodar rplidarA1 com o Ros2

mkdir nome_pasta
cd nome_pasta
mkdir src
colcon build
cd src
git clone https://github.com/allenh1/rplidar_ros.git
cd ..
colcon build --symlink-install
colcon build
source install/local_setup.sh
ros2 launch rplidar_ros view_rplidar.launch.py 
------------------------------------------------------------
Nav2 parece ser um sistema de navegação para Ros2 Humble no meu caso

Processo de instalação (estudar isso)

sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup ros-humble-turtlebot3*

-------------------------------------------------------------
fazendo um mapa

rviz2
ros2 launch ros2_ws/src/rplidar_ros/launch/rplidar.launch.py 
ros2 launch slam_gmapping slam_gmapping.launch.py
ros2 run tf2_ros static_transform_publisher 0 0 0 0 0 0 odom base_link
rosrun tf2_ros static_transform_publisher 0 0 0 0 0 0 base_link laser

--------------------------------------------------------------
Identificação das direções no ROS2

Red - x
Green - y
Blue - z

-----------------------------------------------------------------------------------------------

Tem o passo a passo do que fazer (obs menos criar o arquivo, ou seja tu tem criar a parte)

https://articulatedrobotics.xyz/ready-for-ros-6-tf/

Olhar para corrigir o erro do xacro (obs: estava dando erro pois não tinha o arquivo kkkkk)

crie na dev_ws/src/crie uma pasta chamada 'urdf'

--------------------- Crie um arquivo dentro da pasta criada acima 'xacro_file.xacro'  -------------

<?xml version="1.0"?>
<robot xmlns:xacro="http://www.ros.org/wiki/xacro"  name="robot">

    <material name="grey">
        <color rgba="0.2 0.2 0.2 1"/>
    </material>

    <material name="white">
        <color rgba="1 1 1 1"/>
    </material>

    <material name="orange">
        <color rgba="1 0.3 0.1 1"/>
    </material>

    <material name="blue">
        <color rgba="0 0 1 1"/>
    </material>


    <link name="world"></link>

    <joint name="world_to_base" type="fixed">
        <parent link="world" />
        <child link="base" />
        <origin  rpy="1.57 0 0" xyz="0 0 0" />
    </joint>

    <link name="base">
        <visual>
            <origin xyz="2 0 0" rpy="0 0 0"/>
            <geometry>
                <box size="5 0.1 3" />
            </geometry>
            <material name="grey" />
        </visual>
    </link>


    <joint name="base_to_l3" type="revolute">
        <parent link="base" />
        <child link="l3" />
        <origin  rpy="0 0 0" xyz="0 0.25 0" />
        <axis rpy="0 0 0" xyz="0 0 1" />
        <limit effort="0.1" lower="-3.1" upper="3.1" velocity="0.2"/>
    </joint>


    <link name="l3">
        <visual>
            <origin xyz="0.75 0 0" rpy="0 1.57 0"/>
            <geometry>
                <cylinder length="1.5" radius="0.04" />
            </geometry>
            <material name="orange" />
        </visual>
    </link>

    <joint name="l3_to_l2" type="revolute">
        <parent link="l3" />
        <child link="l2" />
        <!-- <origin xyz="1 0 0" /> -->
        <origin rpy="0 0 0" xyz="1.5 0 0" />
        <axis rpy="0 0 0" xyz="0 0 1" />
        <limit effort="0.1" lower="-3.1" upper="3.1" velocity="0.2"/>
    </joint>


    <link name="l2">
        <visual>
            <origin xyz="0.75 0 0" rpy="0 1.57 0"/>
            <geometry>
                
                <cylinder length="1.5" radius="0.04" />
            </geometry>
            <material name="orange" />
        </visual>
    </link>

    <joint name="l2_to_l1" type="revolute">
        <parent link="l2" />
        <child link="l1" />
        <origin rpy="0 0 0" xyz="1.5 0 0" />
        <axis rpy="0 0 0" xyz="0 0 1" />
        <limit effort="0.1" lower="-3.1" upper="3.1" velocity="0.2"/>
    </joint>


    <link name="l1">
        <visual>
            <origin xyz="0.5 0 0" rpy="0 1.57 0"/>
            <geometry>
                
                <cylinder length="1" radius="0.04" />
            </geometry>
            <material name="orange" />
        </visual>
    </link>


    <joint name="l1_to_grip" type="revolute">
        <parent link="l1" />
        <child link="grip" />
        <origin rpy="0 0 0" xyz="1 0 0" />
        <axis rpy="0 0 0" xyz="0 0 1" />
        <limit effort="0.1" lower="-3.1" upper="3.1" velocity="0.2"/>
    </joint>

    <link name="grip">
        <visual>
            <origin xyz="0.25 0 0"/>
            <geometry>
                <box size="0.5 0.5 0.05" />
            </geometry>
            <material name="white" />
        </visual>
    </link>

    <joint name="world_to_camera" type="fixed">
        <parent link="world" />
        <child link="camera" />
        <origin  rpy="-2 0 2.5" xyz="4 1 2" />
    </joint>

    <link name="camera">
        <visual>
            <origin xyz="0 0 -0.2"/>
            <geometry>
                <box size="0.2 0.2 0.4" />
            </geometry>
            <material name="blue" />
        </visual>
    </link>


</robot>

--------------------------------------------------------------------------------------------------
