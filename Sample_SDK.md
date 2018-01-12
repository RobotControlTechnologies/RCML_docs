[TOC]

# Основные термины
### Класс робот `CPromRobot`
Класс `CPromRobot` используется для управления движением и состоянием робота.
Методами `SetTool`, `GetTool`, `SetBase`, `GetBase`, `SetLoad`, `GetLoad`, осуществляется выбор требуемого инструмента, координатной базы и нагрузки.

### Точки и траектория
Для осуществления движения задается траектория, по которой будет осуществляться движение. Траектория, это объект `CPoints`, который представляет собой набор точек `CPoint`. Траектория может состоять из одной, или более, точек. Точка это объект `CPoint` с набором координат `CCartesianCoordinate`, либо `CJointsCoordinate`. 
Набор координат содержит свойство `Group`, которое определяет принадлежность набора координат устройству. Под устройством понимается робот, позиционер, либо другое устройство. По умолчанию свойство `Group` имеет значение `0`. Нулевая группа координат относится к первому подключенному к контроллеру устройству. Обычно это устройство робот. 
В одном объекте `CPoint` может содержаться несколько наборов координат `CCartesianCoordinate` и `CJointsCoordinate`, с разными значениями свойства `Group`. Таким образом, данной точке `CPoint` все устройства с которыми связаны наборы координат займут положения, описанные этими координатами. Свойства класса `CCartesianCoordinate` описывают декартовы координаты и углы Эйлера. В объекте `CJointsCoordinate`содержится набор объектов `CJoint`. `CJoint` описывает значение угла и номер оси робота, или устройства.
### Управление движением робота
Для осуществления движения робота по заданной траектории `CPoints`, используются методы `MoveLineral`, `MoveCartesian`, `MoveJoint`, `MoveArc`.

- `MoveLineral` осуществляет линейное движение робота по декартовым координатам.
- `MoveCartesian` осуществляет осевое движение робота по декартовым координатам.
- `MoveJoint` осуществляет осевое движение робота до заданного значения углов поворота осей.
- `MoveArc` осуществляет движение по дуге.  

#### Осевое движение:

Все оси начинают движение одновременно и достигают начала замедления так же одновременно.
Траектория движения не является прямой линией. Однако, эта траектория повторяема.
Движение определяется временем, необходимым для каждой оси, чтобы, с заданной скоростью, перейти из текущей позиции в конечную. Для выполнения движения, отводится наибольший отрезок времени из всех, расчитанных отрезков для движения каждой из осей. На протяжении этого времени каждая из осей совершает движение. Ось, для движения которой требуется наибольшее время, двигается с заданной скоростью, тогда как остальные оси двигаются с меньшей скоростью. Это позволяет начинать и заканчивать движение всем осям одновременно.   


#### Линейное движение:

Передвижение TCP робота, из начальной в конечную позицию, с заданной скоростью, происходит по прямой.
Инструмент, по мере движения из начальной точки в конечную, плавно меняет свою ориентацию.

#### Движение по дуге:

Передвижение TCP, из начальной в конечную точку, происходит по дуге.
Дуга строится по трем точкам. Первая точка это текущее положение TCP. Вторая и третья точки - указанные в качестве параметров. При этом, третья точка является конечной точкой траектории.
 
### Управление цифровыми входами и выходами робота
Управление осуществляется при помощи методов `SedDI`, `GetDI`, `GetDO`.
### Управление цифровыми входами и выходами во время движения 
Свойству `MoveControlDO`, объекта `CPoint`, можно присвоить объект `CMoveControlDO`. Свойствами `TriggerType`, `LocationType`, `TriggerValue`, `DO`, `Value` объекта `CMoveControlDO`, описывается когда и какому цифровому выходу устанавливается значение. Свойство `TriggerType` может принимать два значения: `ttDistance` и `ttTime` и описывает когда цифровому выходу должно установиться значение. `ttDistance` - на расстоянии, относительно точки. `ttTime` - за промежуток времени, относительно точки. Расстояние или промежуток времени характеризуется свойством `TriggerValue`. Свойство `LocationType` может принимать два значения: `ltStart` и `ltEnd`, которые описывают до или после точки цифровому выходу будет присвоено значение. 



# Начало работы
Первый шаг - добавить `rcml.h`
```cpp
  #include "rcml.h"
  using namespace RCML;
```

Все классы **RCML SDK** расположены внутри пространства имен `RCML`.

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
   trajectory.AddPoint(new CPoint(new CCartesianCoordinate(100, 200, 50, 0, 90, 0));
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

   myRobot.MoveCartesian(trajectory);
   trajectory.clear();
   trajectory.addPoint(new CPoint(new CCartesianCoordinate(100, 250, 50, 0, 90, 0));  

   myRobot.MoveLineral(trajectory);
```

### Движение осей робота.
Создаем объект Joint, в котором задан угол, на который необходимо передвинуть ось робота. Свойство `Index` содержит номер оси, которую необходимо передвинуть. Создаем точку с джоинтовой координатой. И передвигаем первую ось робота в положение `80` градусов, со скоростью `10`.

```cpp
   CJoint myJoint;
   myJoint.Angle = 80;
   myJoint.Index = 1;

   CPoint* jointPoint = new CPoint(new CJointsCoordinate(myJoint), 10);
   trajectory.clear();
   trajectory.AddPoint(jointPoint);
   myRobot.MoveJoint(trajectory);
```    

# Очередь команд
В выше описанных примерах методы сразу осуществляли управление роботом.
Если необходимо выполнить последовательность действий, то эффективнее сформировать очередь 
команд и инициировать выполнение этой очереди. При использовании очереди команд, промежутки
времени между выполнением команд будут меньше, чем в случае выполнения этих команд по отдельности.
Предположим, что необходимо переместить инструмент `1`, установленный на роботе, по траектории из двух точек
с декартовыми координатами. При этом, требуется линейное движение. Затем, нужно
повернуть шестую ось робота в положение `90` градусов. Далее, выбрать инструмент `2` и переместить его по траектории из трех точек с декартовыми координатами, но дивжением по джоинтам.
Параметр `Que`, метода `MoveLineral`, принимает значение `true`. Это значит, что команда движения по траектории `LinTrajectory` добавится в очередь.

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
# Движение по дуге    
Если необходимо переместить инструмент робота по дуге, можно воспользоваться методом
`MoveArc`. В качестве аргумента нужно передать набор из двух точек. Из движений по дуге, так же, как и из других движений, можно формировать очередь.
```cpp
   CPoints ArcPoints;
   ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(100, 100, 50, 0, 90, 0));
   ArcPoints.AddPoint(new CPoint(new CCartesianCoordinate(200, 0, 50, 0, 90, 0));
   myRobot.MoveArc(ArcPoints);
```

# Управление позиционером
В случае, когда к роботу подключен позиционер, либо другое устройство, имеющее возможность перемещения по координатам или осям.
Чтобы управлять этим устройством, необходимо использовать те же методы движения. Отличие будет лишь в том, что у набора координат нужно указать группу.
Повернем первую ось первого позиционера на `90` градусов.
```cpp
   CJoint Joint(1, 90);
   CJointCoordinates* JCoordinates = new CJointCoordinates(Joint);
   JCoordinates->Group = 1; 

   CPoints JointPoints;
   JointPoints.AddPoint(new CPoint(JCoordinates, 10));
   myRobot.MoveJoint(JointPoints, true);
```
Присвоив значение `1` свойству `Group`, мы указали, что данный набор координат относится к первому позиционеру.
    
# Управление цифровыми входами и выходами робота.
### Установка и считывание состояния
Установка и считывание состояния цифровых входов и выходов робота
```cpp
   myRobot.SetDI(1, 10);
   int DiValue = myRobot.GetDI(10);
   int DoValue = myRobot.GetDO(11);
```
    
### Управление сигналом на траектории.
Добавление к точке триггера, который, при срабатывании, установит значение цифровому выходу.
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

# Описание робота, с использованием интерфейсов из **RCML SDK**.
Интерфейс, описывающий робота, который передвигается только
джоинтовым движением по декартовым координатам.
```cpp
   class CSampleRobot : public CRobot,
  	: public CMoveCartesian{
	 
	public:
	  CCoordinates GetCoordinate();
	  MoveCartesian(CPoints Points, bool Que);
	  MoveCartesianRelative(CPoints Points, bool Que);
   };
```

Требуется самостоятельная реализация указанных выше методов.