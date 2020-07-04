TO COMPILE

1. mkdir catkin_ws
2. cd catkin_ws
3. extract the src folder remove the zip file

To catch any depdencies, I am not sure i have included all the depdencies but i think So
4. rosdep install --from-paths src --ignore-src -r -y

You may want to Run just to be sure 
4.a. sudo apt-get install ros-melodic-ros-control ros-melodic-ros-controllers

5. catkin_make && source devel/setup.bash

TO LAUNCH SIMULATION

1. roslaunch arm_bringup sim_bringup.launch world:=empty   (To launch Gazebo + Controllers)
2. roslaunch arm_bringup moveit.launch                     (To launch Moveit (No need for setting start location though))
3. roslaunch arm_bringup rviz.launch rviz_config:=rviz_sim (To launch RViz with custom config)

TO LAUNCH REALITY

1. roslaunch arm_bringup real_bringup.launch                (urdf + controllers + hardw_interf + transform node)
2. roslaunch arm_bringup moveit.launch
3. roslaunch arm_bringup serial.launch                      (to launch serial if you want include it in real_bringup)
4. roslaunch arm_bringup rviz.launch rviz_config:=rviz_real (to launch RViz with the real config (real and sim config are the same but different))

TIRED OF MANUALLY ENTERING THE COMMANDS IN MULTIPLE TERMINALS??
AND IF YOU PUT ALL IN A LAUNCH FILE -> 1 terminal -> no good debugging -> might get stuck

SOLUTION -> use SCRIPT Files

1. Install prerequisites for xterm emulator

sudo apt-get update -y
sudo apt-get install -y x-terminal-emulator

then 

roscd safe_spawner               (I made this into a package just to be able to use roscd)
ls -a                            
(make sure they appear green or else set the permissions e.g chmod +x safe_spawner_real.sh)

Then you can enter

./safe_spawner_real.sh
./safe_spawner_sim.sh

P.S make sure the python executables also have the permissions (e.g roscd arm_bringup/scripts && chmod +x ...)

Now you can run e.g
 
rosrun arm_bringup pos_exec.py  (For the arm to go to a list of positions consecutevily)
rosrun arm_bringup set_start_pos.py

P.S I Also try to make a this file which you can run by

rosrun arm_bringup moveit_command.py

Which supposedly will add a ground and a box to the planning scene so moveit can avoid it during planning
but i don't know why it doesn't work and i am bored to make the moveit c++ api which i have tested to work P

You can see on my project a package pick_place which uses the moveit c++ api with a service server
e.g recieves the object 3d pos from a client and performs a pick and place, study it to c++ moveit api
and how to use header and cpp files, alongside services, how to add boxes etc

SEE THE MOVEIT TUTORIALS

ARDUINO

The transform node will transform the joint_States into the servo_cmd topic
you can do 
rostopic echo servo_cmd
or if you want to manually publish to the arduino something like this
rostopic pub servo_cmd std_msgs/Int16MultiArray "{layout: {dim: [{label: '', size: 4, stride: 4}], data_offset: 0}, data: [90,90,90,90]}"

I didnt find the need to run the set_start_pos, since the arm starts horizontal
BE CAREFULL!! When rosserial starts the arm will violently go to the horizontal position!!!!
I wasn't sure about the direction of plat_joint (i have made a comment in the transform how to reverse direction) 


GENERAL COMMENTS

I change the transmitions from effort to position
One urdf for real one for sim
On the moveit_config - setup_assistant on ros control click auto add follow traj controller (i put your name and email there)
Study this workspace very good -> see the transform node at arm_bringup/src/pub_to_arduino_class.cpp , the arduino code, the scripts,
the launch files , the CMake files etc..

Files with the prefix TEMPLATE are just for you to look at

Cheers!



