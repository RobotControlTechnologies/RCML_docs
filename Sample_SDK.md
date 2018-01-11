[TOC]

# Начало работы
Первый шаг - добавить **rcml.h**
```cpp
  #include "rcml.h"
  using namespace RCML;
```

Все классы внутри пространства имен **RCML**.

# Задействование подключенного промышленного робота:
```cpp
    CPromRobot myRobot;
```
### Начальная инициализация - задание текущего инструмента, базы и нагрузки.
```cpp
    myRobot.SetTool(2);
    myRobot.SetBase(1);
    myRobot.SetLoad(2);
```

# Движение TCP робота
### Джоинтовое движением в новую точку, в декартовых координатах:
```cpp
    CPoints trajectory;
    trajectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0), 100));
    myRobot.MoveLineral(trajectory);
```

### Перемещение TCP робота джоинтовым движением, а затем линейным движением.
Точки и координаты можно задавать разными способами.
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
```

### Перемещение TCP робота джоинтовым дивжение по декартовым координатам.
```cpp
    myRobot.MoveCartesian(trajectory);
    trajectory.clear();
    trajectory.addPoint(new CPoint(new CCartesianCoordinate(100, 250, 50, 0, 90, 0), 100));
    Здесь мы совершаем линейное дивжение по декартовым координатам.
    myRobot.MoveLineral(trajectory);
```

### Движение осей робота.
Создание объекта Joint, установка значения угла оси робота. Установка номера оси.
```cpp
    CJoint myJoint;
    myJoint.Angle = 80;
    myJoint.Index = 1;
```

### Создание точки с джоинтовой координатой. Передвижение первой оси робота в положение
80 градусов, со скоростью 10.
```cpp
    CPoint* jointPoint = new CPoint(new CJointsCoordinate(myJoint), 10);
    trajectory.clear();
    trajectory.AddPoint(jointPoint);
    myRobot.MoveJoint(trajectory);
```    

# Очередь команд
В выше описанных примерах, методы сразу осуществляли уравление роботом.
Если необходимо выполнить последовательность действий, то эффективнее сформировать очередь 
команд и инициировать выполнение этой очереди. При использовании очереди команд, промежутки
времени между выполнением команд будут меньше, чем в случае выполнения этих команд по отдельности.
Предположим, что необходимо переместить инструмент 1, установленный на роботе, по траектории из двух точек
с декартовыми координатами. При этом, требуется линейное движение. Затем, нужно
повернуть ось робота 6 в положение 90 градусов. Далее, выбрать инструмент 2 и переместить его по траектории из трех точек с декартовыми координатами, но дивжением по джоинтам.
```cpp
    myRobot.SetTool(1, true);
    CPoints LinTragectory;
    LinTragectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0), 100));
    LinTragectory.AddPoint(new CPoint(new CCartesianCoordinate(200, 200, 50, 0, 90, 0), 100));
    myRobot.MoveLineral(LinTragectory, true);
```
### Параметр `Que`
Параметр `Que`, метода `MoveLineral`, принимает значение `true`. Это значит, что команда движения по траектории `LinTrajectory` добавится в очередь.
```cpp
    CJoint Joint(6, 90);
    CJointCoordinates* JCoordinates = new CJointCoordinates(Joint);
    CPoints JointPoints;
    JointPoints.AddPoint(new CPoint(JCoordinates, 10));
    myRobot.MoveJoint(JointPoints, true);
    
    myRobot.SetTool(2, true);
    
    CPoints JointTragectory;
    JointTragectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0), 100));
    JointTragectory.AddPoint(new CPoint(new CCartesianCoordinate(200, 200, 50, 0, 90, 0), 100));
    JointTragectory.AddPoint(new CPoint(new CCartesianCoordinate(300, 200, 50, 0, 90, 0), 100));
    myRobot.MoveJoint(JointTragectory, true);
```
### Движение по дуге    
Если необходимо переместить инструмент робота по дуге, можно воспользоваться методом
`MoveArc`. В качестве аргумента нужно передать набор из трех точек. Из движений по дуге, так же, как и из других движений, можно формировать очередь.
```cpp
    CPoints ArcPoints;
    ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(0, 0, 50, 0, 90, 0), 100));
    ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(100, 100, 50, 0, 90, 0), 100));
    ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(200, 0, 50, 0, 90, 0), 100));
    myRobot.MoveArc(ArcPoints);
```

### Управление позиционером
В случае, когда к роботу подключен позиционер, либо другое устройство, имеющее возможность перемещения по координатам или осям.
Чтобы управлять этим устройством, необходимо использовать те же методы движения. Отличие будет лишь в том, что у набора координат нужно указать группу.
Повернем первую ось первого позиционера на 90 градусов.
```cpp
    CJoint Joint(1, 90);
    CJointCoordinates* JCoordinates = new CJointCoordinates(Joint);
    JCoordinates->Group = 1; 
    Присвоив значение 1 свойству Group, мы указали, что данный набор координат относится к первому позиционеру.
    CPoints JointPoints;
    JointPoints.AddPoint(new CPoint(JCoordinates, 10));
    myRobot.MoveJoint(JointPoints, true);
```
    
### Управление цифровыми входами и выходами робота.
Установка и считывание состояния цифровых входов и выходов робота
```cpp
    myRobot.SetDI(1, 10);
    int DiValue = myRobot.GetDI(10);
    int DoValue = myRobot.GetDO(11);
```
    
### Добавление к точке триггера, который, при срабатывании, установит значение цифровому выходу.
```cpp
    CMoveControlDO MoveControlDO;
    MoveControlDO.TriggerType = CTriggerType::ttDistance;
    MoveControlDO.LocationType = CLocationType::ltEnd;
    MoveControlDO.TriggerValue = 25;
    MoveControlDO.Do = 10;
    MoveControlDO.Value = 1;
    CPoint* somePoint = new CPoint();
    somePoint.MoveControlDO = MoveControlDO;
```
    
# Создание класса, наследника `CPromRobot`
Создание класса, наследника `CPromRobot`, с четырьмя методами. Два метода для управления вакуумным захватом - включение и выключение захвата. И два метода, при помощи которых будет осуществляться перенос предмета Brick, из точки, в корзину Basket. Передвижение захвата будет осуществляться за счет изменения положений осей робота.
```cpp
	    class СVacuumRobot : public CPromRobot
	    {
	    public:
	       void vacuumOn(){
	       SetRO(4, 0);
	       SetRO(7, 1);
	       };
	       
	       void vacuumOff(){
	       SetRO(4, 1);
	       SetRO(7, 0);
	       };
	       
	    void goToBrick(){
		     int speed = 100;
		     int cnt = 0;
		     CMoveControlDO moveControlDO;
		     CJointCoordinate brickCoords = new CJointCoordinate();
		     brickCoords->AddJoint(CJoint(1, 0), CJoint(2, 90), CJoint(3, 90), CJoint(4, 180), CJoint(5, -20), CJoint(6, 90));
		     
		     CPoint pointBrick = new CPoint();
		     pointBrick->SetMoveControlDO(moveControlDO);
		     pointBrick->SetTool(2);
		     pointBrick->SetBase(1);
		     pointBrick->SetLoad(2);
		     pointBrick->Speed = speed;
		     pointBrick->Cnt = cnt;
		     pointBrick->Coordinate = brickCoords;
		     
		     
		     CPoints trajectory;
		     trajectory.AddPoint(pointBrick);
		     
		     JointMove(trajectory);
	      };
	       
	    void goToBasket(){
	     int speed = 80;
	     int cnt = 0;
	     CMoveControlDO moveControlDO;
	     CJointCoordinate basketCoords = new CJointCoordinate();
	     basketCoords->AddJoint(CJoint(1, 0), CJoint(2, 180), CJoint(3, 24), CJoint(4, 55), CJoint(5, 0), CJoint(6, 0));
	     basketCoords->SetTool(2);
	     basketCoords->SetBase(1);
	     basketCoords->SetLoad(2);
	     basketCoords->Speed = speed;
	     basketCoords->Cnt = cnt;
	     basketCoords->Coordinate = brickCoords;
	     
	     CPoint pointBasket = new CJointCoordinate();
	     CPoints trajectory;
	     trajectory.AddPoint(pointBasket);
	     
	     JointMove(trajectory);
	    };
	  };
```


Перенос кирпич в корзину.
```cpp
    СVacuumRobot myVacuumRobot;
    myFanuc.goToBrick();
    myFanuc.vacuumOn();
    myFanuc.goToBasket();
    myFanuc.vacuumOff();
```
    

# Описание робота, с использованием интерфейсов из **RCML SDK**.
Интерфейс, описывающий робота, который передвигается только
джоинтовым движением по декартовым координатам.
```cpp
	class CSampleRobot : public CRobot,
	   : public CMoveCartesian{
	 
		public:
		CCoordinates GetCoordinate(){ };
		MoveCartesian(CPoints Points, bool Que){ //Реализация };
		MoveCartesianRelative(CPoints Points, bool Que){ //Реализация };
	};
```

Требуется самостоятельная реализация методов.