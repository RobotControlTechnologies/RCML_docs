[TOC]

# The main terms
### Class of robot `CPromRobot`
The `CPromRobot` class is used for movenment contorl and state of robot.
Methods `SetTool`, `GetTool`, `SetBase`, `GetBase`, `SetLoad`, `GetLoad`, is used for tool, coordinate base and load choosing.

### Points and trajectory
For robot moving defines by the trajectory. Trajecotry is the object `CPoints`. The `Cpoints` object is the list of `CPoint` objects. Trajecotry may contain onw or more points. The `CPoint` includes set of coordinate like `CCartesianCoordinate`, or `CJointsCoordinate` objects. 
The set of coordinate inludes property `Group`. It defines identity set of coordinate to device. Deviece is the robot, or positioner or the other device. By default the `Group` property is `0`. Group number zero relates with the first connected device. Usually this device is a robot. 
`CPoint` object may contain multiple sets of `CCartesianCoordinate` and `CJointsCoordinate` with defferent `Group` properties. Thus in `CPoint` point all devices will be moved in each coordinate binded by `Group` property. The properties of `CCartesianCoordinate` class describe cartesian coordinates and Euler angles. `CJointsCoordinate` object contain set of `CJoint` objects. `CJoint` object describes angle of robot or device axis and number of robot or device axis.
### Control of robot movement
For robot movement along the `CPoints` trajectory `MoveLineral`, `MoveCartesian`, `MoveJoint`, `MoveArc` methodes are used.

- `MoveLineral` starts lineral movement in cartesian coordinates.
- `MoveCartesian` starts joint movement in cartesian coordinates.
- `MoveJoint` starts joint movement to assigned angles of robot axises.
- `MoveArc` starts arc movement.  

#### Joint movement:

Все оси начинают движение одновременно и достигают начала замедления так же одновременно.
Траектория движения не является прямой линией. Однако, эта траектория повторяема.
Движение определяется временем, необходимым для каждой оси, чтобы с заданной скоростью перейти из текущей позиции в конечную. Для выполнения движения отводится наибольший отрезок времени из всех рассчитанных отрезков для движения каждой из осей. На протяжении этого времени каждая из осей совершает движение. Ось, для движения которой требуется наибольшее время, двигается с заданной скоростью, тогда как остальные оси двигаются с меньшей скоростью. Это позволяет начинать и заканчивать движение всем осям одновременно.   


#### Линейное движение:

Передвижение TCP робота из начальной в конечную позицию с заданной скоростью происходит по прямой.
Инструмент, по мере движения из начальной точки в конечную, плавно меняет свою ориентацию.

#### Движение по дуге:

Передвижение TCP из начальной в конечную точку происходит по дуге.
Дуга строится по трем точкам. Первая точка - это текущее положение TCP. Вторая и третья точки - указанные в качестве параметров. При этом, третья точка является конечной точкой траектории.
 
### Control of digital robot inputs and outputs.
For contorol `SedDI`, `GetDI`, `GetDO` methodes are used.
### Contorl of digital robot inputs and outputs during the motion 
!!!!`CMoveControlDO` may assigned to `MoveControlDO` property of `CPoint` object. `TriggerType`, `LocationType`, `TriggerValue`, `DO`, `Value` properties of object `CMoveControlDO` describe when and which value will be assigned to the digital output . `TriggerType` property may have `ttDistance` or `ttTime` value. It describes when digital output will be assigned the value. `ttDistance` describes on which distance digital output will be assigned the value. `ttTime` describes time interval. `TriggerValue` property is the value of time interval or distance. `LocationType` may have `ltStart` or `ltEnd` value. It describes  location (before or after point) the value will be assigned to the digital output. 



# Starting
The first step is to include `rcml.h`
```cpp
  #include "rcml.h"
  using namespace RCML;
```

All class of **RCML SDK** are locate in `RCML` namespace.

# Using of factory robot connected by controller:
```cpp
   CPromRobot myRobot;
```
### Initialization of robot. Choosing of current tool, base and load.
```cpp
   myRobot.SetTool(2);
   myRobot.SetBase(1);
   myRobot.SetLoad(2);
```

# Movement of robot TCP
### Joint movement to the point by cartesian coordinates:
```cpp
   CPoints trajectory;
   trajectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0));
   myRobot.MoveLineral(trajectory);
```

### Joint and lineral movement of robot TCP .
There are different ways to assign points and coordinates.
```cpp
   trajectory.clear();
   CCartesianCoordinate* coordinateJoint = new CCartesianCoordinate();
   coordinateJoint->X = 100;
   coordinateJoint->Y = 200;
   coordinateJoint->Z = 50;
   coordinateJoint->A = 0;
   coordinateJoint->B = 90;
   coordinateJoint->C = 0;
    
   CPoint *jointPoint = new CPoint();
   jointPoint->Coordinates.Add(coordinateJoint);
   trajectory.addPoint(jointPoint);

   myRobot.MoveCartesian(trajectory);
   trajectory.clear();
   trajectory.addPoint(new CPoint(new CCartesianCoordinate(100, 250, 50, 0, 90, 0));  

   myRobot.MoveLineral(trajectory);
```

### Movement of robot axises.
Create `Joint` object with angle value for axis. `Index` property is number of robot axis. Create point with joint coordinate. Move first robot axis in `80` degrees position. Speed of movement is `10`.

```cpp
   CJoint myJoint;
   myJoint.Angle = 80;
   myJoint.Index = 1;

   CPoint* jointPoint = new CPoint(new CJointsCoordinate(myJoint), 10);
   trajectory.clear();
   trajectory.AddPoint(jointPoint);
   myRobot.MoveJoint(trajectory);
```    

# Command queue
In the previous cases, the methodes controled robot immediately. Sequence of methodes calls will be accomplished faster if they will be pushed in the queue.
The time intervals between commands in queue will be shorter than intervals between commands out of queue.
The tool installed on the robot moves by two cartesian coordinate points. The first movement is lineral. Then the sixth axis of robot will be turned to `90` degrees. Then choose tool number `2` and move it by thre points trajectory by cartesian coordinate.
The `Queue` property value of `MoveLineral` method is `true`. It means that the command of movement will be pushed to the queue.

```cpp
   myRobot.SetTool(1, true);
   CPoints LinTragectory;
   CPoint::SetDefSpeed(100);
   LinTragectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0));
   LinTragectory.AddPoint(new CPoint(new CCartesianCoordinate(200, 200, 50, 0, 90, 0));
   myRobot.MoveLineral(LinTragectory, true);

   CJoint Joint(6, 90);
   CJointCoordinates* JCoordinates = new CJointCoordinates(Joint);
   CPoints JointPoints;
   JointPoints.AddPoint(new CPoint(JCoordinates, 10));
   myRobot.MoveJoint(JointPoints, true);
    
   myRobot.SetTool(2, true);
    
   CPoints JointTragectory;
   JointTragectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0)));
   JointTragectory.AddPoint(new CPoint(new CCartesianCoordinate(200, 200, 50, 0, 90, 0)));
   JointTragectory.AddPoint(new CPoint(new CCartesianCoordinate(300, 200, 50, 0, 90, 0)));
   myRobot.MoveJoint(JointTragectory, true);
```
# Arc movement    
Use `MoveArc` method for arc movement.
`MoveArc` method expects two point as argument. Arc movement and other movement may be pushed in the queue.
```cpp
   CPoints ArcPoints;
   ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(100, 100, 50, 0, 90, 0));
   ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(200, 0, 50, 0, 90, 0));
   myRobot.MoveArc(ArcPoints);
```

# Positioner control
If the positioner or another device, which able to move by coordinate, is connected to controller, same methodes are used to control it.  
Notice, that `Group` property of coordinate set, should be assigned.
Turn the first axis of the first positioner to `90` degrees.
```cpp
   CJoint Joint(1, 90);
   CJointCoordinates* JCoordinates = new CJointCoordinates(Joint);
   JCoordinates->Group = 1; 

   CPoints JointPoints;
   JointPoints.AddPoint(new CPoint(JCoordinates, 10));
   myRobot.MoveJoint(JointPoints, true);
```
The value of `Group` property is `1`, that means the set of coordinate binded to the first positioner.
    
# Control of digital robot inputs and outputs.
### Reading and setting of state
Reading and setting state of digital inputs and outputs 
```cpp
   myRobot.SetDI(1, 10);
   int DiValue = myRobot.GetDI(10);
   int DoValue = myRobot.GetDO(11);
```
    
### Contorl of digital signal during the motion.
The trigger, which added to the point, will assign the digital output value.
```cpp
   CMoveControlDO MoveControlDO;
   MoveControlDO.TriggerType = CTriggerType::ttDistance;
   MoveControlDO.LocationType = CLocationType::ltEnd;
   MoveControlDO.TriggerValue = 25;
   MoveControlDO.Do = 10;
   MoveControlDO.Value = 1;
   CPoint* somePoint = new CPoint();
   somePoint.MoveControlDO = MoveControlDO;
   
   CPoints myTragectory;
   CPoint* myPoint = new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0);
   myPoint->MoveControlDO = MoveControlDO;
   LinTragectory.AddPoint(myPoint);
   myRobot.MoveLineral(myTragectory, true);
```    

# Description of robot, using **RCML SDK** interfaces.
The interface describes robot, which able to use joint movement by cartesian coordinate.
```cpp
   class CSampleRobot : public CRobot,
  	: public CMoveCartesian{
	 
	public:
	  CCoordinates GetCoordinate();
	  MoveCartesian(CPoints Points, bool Que);
	  MoveCartesianRelative(CPoints Points, bool Que);
   };
```

Notice, that `GetCoordinate`, `MoveCartesian` and `MoveCartesianRelative` methodes demand custom realization.
