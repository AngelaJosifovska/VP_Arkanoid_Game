 ArkanoidGame
   
   Станува збор за поедноставна имплементација на популарната игра Arkanoid во која целта е со помош на палката да се контролираат ударите врз топчето и да се погодуваат објектите т.е. цигличките.
      При стартување на апликацијата најпрво се појавува MessageBox преку кој играчот може да одлучи дали играта сака да ја стартува full screen или не.Потоа се појавува главното мени во кое играчот може да одбере да стартува нова игра,да одбере на кој начин ќе ја контролира играта(преку лев клик на втората опција,дали палката ќе ја контролира со движење на глувчето или со стрелките од тастатурата)  или пак дали сака да ја затвори апликацијата.Со избирање на опцијата за стартување започнува играта.Во долниот дел се наоѓа палката која играчот треба да ја кнтролира.Со лев клик на глувчето се испалуваат три топчиња.Целта е да се погодуваат цигличките.Постојат два типа на циглички: правоаголни и квадратни.Правоаголните за да бидат избришани потребно е да бидат погодени два пати и затоа нивното бришење носи два пати повеќе поени.Квадратните циглички исчезнуваат со едно нивно погодување.Овие циглички се наредени во редици во горниот дел,а цигличките во средниот дел ротираат со цел да ја отежнат играта и да ја направат поинтересна.Играта завршува кога ќе се изгубат сите три топчиња.
     Со притискање на Esc се отвара ново мени во кое може да се одбере да се продолжи старата игра,онаму каде играчот застанал,може да се стартува нова игра или да се затвори апликацијата.



Имплементацијата е следна:
     Формата GameWindow всушност ја претставува главната форма,т.е. формата во која се прикажува самата апликација.
    Класата GameFramework e онаа која врши синхронизација на времето и брои колку рамки(слики) во секунда се рендерираат и истото го контролира.Предвидениот број на слики кои треба да се рендерираат е 60.Соодветно оваа класа го зема graphics-от на панелот од GameWindow и го повикува неговиот OnDraw метод.Оваа класа чува инстанца од класата Game која мора да го имплементира IGame интерфејсот.Во методот StartGame се стартува играта пратена како аргумент во конструкторот.Целата синхронизација се состои во следното:
    Во еден бесконечен циклус во методот GameMainLoop постојано се врши проверка дали настанала грешка.Доколку овој метод врати број различен од сто настанала грешкаи се излегува од апликацијата.Во спротивен случај се повикува update на инстанцата од Game што значи дека играта сеуште трае.Тука се врши синхронизација на времето.Се бројат преиодите кои поминале од последниот update на играта,при што секој период трае фиксни 16ms.Ако времето во играта кое се пресметува како број на периоди од update  на играта задоцнува зад реалното време не се прави Invalidate,се исклучува рендерирањето се додека не се достигне реалното време.Ако времето во играта забрзува,забрзува за онолку колку што останало до update  и со тоа време како аргумент се прави Thread.Sleep.Целта е растојанието кое го минуваат објектите да не зависи од бројот на рамки во секунда.На крајот се проверува дали апликацијата треба да се исклучи или не.

   Потоа класата GameArkanoid ја претставува самата игра.Таа го имплементира интерфејсот IGame .Тука се одлучува дали играта ќе се извршува на повеќе нишки или не,во зависност од тоа дали е притиснато М.Се креира играта и се поставува во состојба ArkanoidStateMainMenu во која всушност се прикажува главното мени.потоа се поставуваат димензиите и на крај го има својството IsTimesynchronizationImportant кое доколку врати true значи дека ќе мора да се исконтролира рендерирањето(објаснето погоре во GameFramework).
    
ArkanoidMainMenuState е класа што ја претставува состојбата на играта кога се прикажува главното мени. ArkanoidPauseMenuState е класа која ја претставува состојбата кога играта е паузирана односно после играње е притиснато Еsc.И двете го имплементираат интерфејсот IGameState.Прикажувањето на менијата е објаснето со коментари во самиот код.
  ArkanoidGamePlayState ја претставува состојбата на играта додека таа трае т.е. откако ќе се стартува.
                Во неа се инстанцираат и се повикува исцртувањето на сите објекти(палката,топчето и цигличките).Методот EnableOrDisableDebugMode одлучува дали на екранот ќе се прикажат сите правоаголници кои поради QuadTree-то од кое се чува инстанца ќе ги прикаже регионите на судир на секој од објектите.DebugMode се постигнува со притискање на D.
               Со методот UpdateObject се повикува методот OnUpdate на конкретен објект.Најважен е методот OnUpdate koj го повикува OnUpdate на секој објект,но постои опција дали тоа ќе се прави во една или повеќе нишки (детално е објаснето со коментари) и соодветно ги додава во листа битмапите на сите објекти кои треба да се рендерираат.
              Методот InitQuadtree го пребришува QuadTree-то и го обновува.Во него се додаваат сите објекти.
              Во методот CheckForCollisions се прави quaery од сите објекти кои се наоѓаат во регионот на судири на објектот кој се предава како аргумент (секој објект генерира минимален правоаголник кој го претставува регионот во кој можат да настанат судири со други објекти).Потоа се прави проверка за судири на секој со секој објект од query-то и се добива листа од точки (пресечни) доколку постојат судири на објекти .На секој објект му се испраќа HashMap која како клучеви ги содржи објектите со кои се судрил(можат да се видат обоени со црвено со притискање на D),а како вредности точките во кои се сечат(можат да се видат обоени со сино со притискање на D).За оваа цел искористена е готова структура QuadTree (во директориумот QuadTree).
             
Во директориумот Interfaces се поставени сите интерфејси кои ги содржат соодветните методи.Интерфејсот IGame е имплементиран од GameArkanoid,IGameObject е имплементиран од соодветните класи кои ги претставуваат соодветните објекти.IGameRenderer е имплементиран од GameRenderer кој всушност се грижи за усогласување на димензиите на играта со димензиите на прозорецот на кој таа се прикажува.

   Класата RendererCache соодветно во меморија чува битмапи претставени со помош на инстанци на класата GameBitmap  кои имаат соодветно свои ID (детално објаснето со коментари во кодот).

   Во директориумот Geometry се дадени класи кои соодветно претставуваат дводимензионални вектори(употребувани секаде каде што се потребни координати) и тродимензионални вектори.Во класата GameRectangle се наоѓаат методите за проверка на пресеците на објект со облик на правоаголник со останати објекти.Во класата GeometricAlgoritms се наоѓаат методи за пресек на објекти со облик на круг со други објекти. Во класата GameCircle се наоѓа методот за проверка на пресек на круг со круг. Овде се искористени готови математички методи за наоѓање на пресечните точки.

   Во директориумот Resources се наоѓаат сликите кои се користат за исцртување на објектите.
   
   Во директориумот Objects се наоѓаат класите AbstarctBrick од која наследуваат класата BigBrick за правоаголните циглички и SmallBrick за квадратните циглички.Секоја од наследените класи си има свој соодветен конструктор.И двете ги имплементираат методите OnUpdate и OnColisionDetected.BigBrick имаат својство Health од 200 кое со првиот удар се намалува на 100,а со вториот на 0.SmallBrick  има својство  Health 100 и уште со првиот удар се намалува на нула,циглата се отстранува.Од ваквото својство зависи кога ќе бидат избришани.Класата BlueBall е класата на топчето,таму кога својството DamageEffect изнесува 100 значи го намалил Health циглата со која се удрил за 100.Класата PlayerPaddle ja претставува палката.Сите го имплементираат интерфејсот IGameObject.Директориумот DotNetMatrix содржи готови класи. 

      

 Детално кодот е објаснет со соодветни коментари во секоја од класите.

