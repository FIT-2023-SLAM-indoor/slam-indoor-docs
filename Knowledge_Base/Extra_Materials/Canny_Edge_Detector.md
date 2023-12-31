# Canny edge detector

Created: October 4, 2023 6:28 PM

## Что такое градиент?

**Градиент** - это вектор, который показывает направление наибольшего прироста функции в данной точке.

Проще говоря, представьте градиент для функции двух переменных f(x,y) в двумерном пространстве. Вот у нас есть поверхность в трехмерном пространстве, заданная функцией f(x,y). В каждой точке (x,y) на этой поверхности можно взять частные производные функции по x и по y: ∂f/∂x и ∂f/∂y

Эти частные производные показывают, как быстро меняется функция f при небольшом изменении x или y соответственно.

Градиент функции в точке (x,y) - это вектор из этих двух производных: grad f(x,y) = (∂f/∂x, ∂f/∂y)

Геометрически градиент указывает направление наискорейшего возрастания функции в данной точке.

Например, если в точке (2,3) градиент равен (1, 5), то это значит, что при небольшом приращении x функция вырастет на 1, а при таком же приращении y - на 5. То есть направление наиболее крутого подъёма - преимущественно вдоль оси y.

Таким образом, градиент даёт локальную информацию о поведении функции в окрестности точки.

## Что такое фильтр Гаусса?

Разберём подробно сглаживание фильтром Гаусса. Представим, что у нас есть пиксель на изображении с каким-то значением яркости (допустим, 150).

Чтобы сгладить изображение, мы берём этот пиксель и рассматриваем его соседей - скажем, 8 пикселей вокруг него. Далее мы вычисляем взвешенное среднее значение яркости этих 9 пикселей (исходный + 8 соседей). Но веса мы задаём не одинаковые, а согласно функции Гаусса.

Эта функция присваивает наибольший вес центральному пикселю, а веса соседних пикселей уменьшаются по мере удаления от центра. То есть соседи "дальше" от центра влияют меньше.

Таким образом мы получаем средневзвешенное значение яркости для центрального пикселя, которое и записываем как результат сглаживания. В итоге изображение немного "размывается", шум сглаживается, а основные края и формы сохраняются благодаря бОльшим весам центральных пикселей.

Я немного упростил объяснение, рассматривая пиксель как просто число яркости. На самом деле:

- Пиксель на цветном изображении задаётся тремя значениями - красным, зелёным и синим (RGB).
- При сглаживании мы применяем фильтр Гаусса отдельно к каждому из этих трёх цветовых каналов.

То есть для красного цвета берём яркость красного канала в пикселе и его соседях, вычисляем взвешенное среднее. Затем то же самое делаем для зелёного и синего каналов.

В итоге для каждого пикселя получаем три сглаженных значения - для красного, зелёного и синего цветов. Их комбинация и определяет новый цвет этого пикселя после сглаживания.

---

# Что такое Canny edge detector?

Canny edge detector - это алгоритм в компьютерном зрении для обнаружения границ объектов на изображении. Основные этапы его работы:

1. ***Сглаживание изображения*** фильтром Гаусса (на самом деле Canny может использовать разные фильтры сглаживания). При этом каждый пиксель заменяется взвешенным средним своих соседей. Веса задаются функцией Гаусса. Это позволяет убрать шум и несущественные детали.
2. ***Вычисление градиента изображения**.* Градиент изображения находится посредством вычисления производных по X и Y (это координаты пикселя на изображении). Производные берем от функции f(x; y), значение которой характеризует цвет этого пикселя. Градиент показывает интенсивность изменения изображения в данной точке. Так мы определяем направления и величины максимального изменения интенсивности в каждой точке. [https://habr.com/ru/articles/114452/](https:)
    
    На этом этапе часто применяют оператор Собеля (его я увидел в OpenCV туториале). Применяется для вычисления аппроксимации градиента (то есть приближенного значения градиента) изображения. Для вычисления градиента изображения используются дифференциальные алгоритмы, такие как операции Собеля или другие. Они позволяют вычислить градиент по горизонтальному и вертикальному направлениям на основе изменений яркости пикселей в тех областях пространства, которые окружают каждый пиксель изображения.
    
3. ***Подавление не максимальных значений градиента(MAX PULLING).*** На этом шаге у нас уже есть: 1)Сглаженное изображение; 2)Вычисленный градиент в каждой точке (у нас уже есть изображение градиентов - в каждой точке знаем направление градиента и его величину)
    
    Minimum cut-off suppression значений градиента, или нижнее пороговое ограничение, является методом утоньшения краев. Находим участки с наиболее резким изменением значений интенсивности. Алгоритм для каждого пикселя в изображении градиента выглядит следующим образом:
    
    1. Сравнить величину градиента текущего пикселя с величинами градиента пикселей в положительном и отрицательном направлениях градиента. Более простыми словами: Мы берём точку с неким направлением градиента (например, вверх-вниз). Затем рассматриваем две соседние точки по этому направлению (сверху и снизу). Сравниваем величину градиента в рассматриваемой точке с величинами градиентов в этих двух соседних точках. При этом направления градиентов в соседних точках могут быть любыми, не обязательно совпадающими.
    2. Если величина градиента текущего пикселя является наибольшей по сравнению с другими пикселями в маске с тем же направлением, значение будет сохранено. В противном случае значение будет подавлено.
    
    [*Далее источник [https://en.wikipedia.org/wiki/Canny_edge_detector](https://https//)]
    
    В некоторых реализациях алгоритм категоризирует непрерывные направления градиента в небольшое количество дискретных направлений, а затем перемещает фильтр 3x3 по результатам предыдущего шага (то есть величины градиента и его направления). В каждом пикселе он подавляет величину градиента центрального пикселя (устанавливая его значение в 0), если его величина не больше величины двух соседних пикселей в направлении градиента.
    
    **Например:**
    
    [*Далее знак направления не имеет значения, т.е. север-юг эквивалентен юг-северу и т.д.]
    
    - Если округленное направление градиента равно 0° (т.е. край направлен с севера на юг), точка будет считаться лежащей на краю, если её величина градиента больше величин в направлениях **восток** и **запад.**
    - **Е**сли округленное направление градиента 90° (т.е. край направлен с запада на восток), точка будет считаться лежащей на краю, если её величина градиента больше величин в направлениях **север** и **юг.**
    - **Е**сли округленное направление градиента 135° (т.е. край направлен с северо-востока на юго-запад), точка будет считаться лежащей на краю, если её величина градиента больше величин в направлениях **северо-запад** и **юго-восток.**
    
    В более точных реализациях используется линейная интерполяция между двумя соседними пикселями, которые охватывают направление градиента. Например, если угол градиента находится между 89° и 180°, интерполяция между градиентами в пикселях **север** и **северо-восток** даст одно интерполированное значение, а интерполяция между пикселями **юг** и **юго-запад** даст другое значение (согласно обозначениям из предыдущего пункта). Величина градиента в центральном пикселе должна быть больше обоих этих значений, чтобы он был отмечен как край.
    
4. ***Двойная пороговая фильтрация.*** На этом этапе к изображению градиентов, полученному после подавления слабых значений, применяется пороговая фильтрация дважды:
    1. Сначала по верхнему порогу - все точки с градиентом выше этого порога обнуляются (удаляются как сильные градиенты).
    2. Затем по нижнему порогу - обнуляются точки, где градиент ниже этого порога (удаление слабых градиентов).
    3. Точки между порогами пока сохраняются на изображении и могут использоваться на следующих этапах для восстановления разрывов границ.
    
    В итоге после двойной пороговой фильтрации мы получаем изображение, содержащее только области со средними значениями градиента - предполагаемые границы объектов.
    
    Порог для подавления задаётся заранее при настройке алгоритма.
    
5. ***Трассировка границ*** по оставшимся пикселям градиента.
    
    Я попробую описать трассировку границ максимально просто. Алгоритм:
    
    1. Выбирается произвольная точка на границе как стартовая.
    2. Далее трассируется контур, последовательно переходя от точки к точке по направлению градиента.
    3. Если возникает разрыв границы, используются эвристики для поиска следующей точки контура.
    4. Процесс повторяется до замыкания контура или выхода за пределы изображения.
    5. Затем выбирается следующая стартовая точка и трассируется новый контур.
    
    В результате получаются замкнутые контуры, соответствующие границам объектов на изображении.
    
    Этот этап завершает работу алгоритма Canny и позволяет выделить искомые границы.
    

---

## Подробнее про оператор Собеля:

Операция Собеля применяется путем свертки изображения с двумя малыми, линейными фильтрами по горизонтали и вертикали. Свертка изображения - это математическая операция, при которой применяется фильтр (матрица) к каждому пикселю изображения. Фильтр проходит по каждому пикселю и умножает этот пиксель и его окрестность на соответствующие значения в фильтре. Затем суммируются все полученные значения, и результат помещается в новый пиксель. Эти фильтры содержат коэффициенты, которые при умножении на значения пикселей в окрестности дают взвешенную сумму, позволяющую определить, насколько сильно значение пикселя отличается от соседних. [https://en.wikipedia.org/wiki/Sobel_operator](https://en.wikipedia.org/wiki/Sobel_operator)

Результатом операции Собеля является изображение, в котором границы объектов выделены тонкими линиями.

И оператор Собеля, и фильтр Гаусса используют свертку изображения, но их применение и цель немного различаются.

Фильтр Гаусса применяется для размытия изображения. Он применяет гауссово ядро (матрицу коэффициентов), которая имеет распределение Гаусса, к каждому пикселю изображения. Это позволяет размыть или сгладить резкие переходы между пикселями, уменьшить шум и улучшить общую визуализацию. Часто он используется в предварительной обработке изображений перед другими алгоритмами, такими как обнаружение краев или идентификация объектов.

Оператор Собеля, с другой стороны, используется для обнаружения границ в изображении. Он применяет два фильтра (один для горизонтальной, другой для вертикальной компоненты) к каждому пикселю изображения. Эти фильтры определяют градиент яркости в окрестности пикселя и позволяют выделить резкие изменения яркости или контраста, что обычно свидетельствует о наличии границы или края объектов.

Таким образом, хотя оба метода основаны на свертке изображения, их применение и результаты различаются. Фильтр Гаусса используется для сглаживания и улучшения изображения, тогда как оператор Собеля используется для обнаружения границ и краев.

## Различия между 3 и 4 этапом:

Эти этапы алгоритма Canny имеют схожую цель - удаление слабых и незначимых значений градиента, но делают это разными способами:

- Третий этап - подавление не максимальных градиентов. Сравнивается градиент каждой точки с соседями в направлении градиента. Если градиент не максимален - обнуляется. Удаляет шумовые и слабые градиенты.
- Четвертый этап - двойная пороговая фильтрация. Значения градиентов сравниваются с верхним и нижним порогами. Градиенты выше верхнего и ниже нижнего порогов обнуляются. Позволяет убрать сильные и слабые градиенты.

**Основные различия:**

- Третий этап локально анализирует точку относительно соседей.
- Четвертый этап сравнивает глобально со значениями порогов.
- Третий этап более точный, но есть риск удаления мелких деталей.
- Четвертый этап проще реализуется, но требует подбора порогов.

## Преимущества детектора Canny:

- Хорошее обнаружение границ при минимуме ошибок. Оптимальный детектор должен исключать возможность получения ложноположительных и ложноотрицательных результатов.
- Локализация границ в один пиксель. Обнаруженные края должны быть как можно ближе к истинным краям.
- Одиночный отклик на одну границу. Детектор должен возвращать только одну точку для каждой граничной точки.