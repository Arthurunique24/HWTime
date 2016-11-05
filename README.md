#Глава 36. Boost.DateTime
#### Содержание 
[Календарные даты](#CalendarDates)

[Время независящее от места](#LocationIndependentTimes)

[Время зависязее от места](#LocationDependentTimes)

[Форматированный ввод и вывод](#FormattedInputAndOutput)

Библиотека [Boost.DateTime](http://www.boost.org/doc/libs/1_62_0/doc/html/date_time.html) может быть использована для обработки временных данных, таких как календарные даты и время. Кроме того, Boost.DateTime предоставляет расширения для ля учета часовых поясов и поддерживает форматированный ввод и вывод календарных дат и времени. Если вы ищете функции для получения текущего времени или измерения текущего времени, смотри Boost.Chrono в [Главе 37](https://theboostcpplibraries.com/boost.chrono)

---
<a name="CalendarDates"></a>
#Календарные даты 
Boost.DateTime поддерживает только кадендарные даты на основе Григорианского календаря, который совершенно не является проблемой, так как это наиболее это наиболее широко используемый календарь. Если вы организовываете встречу с кем-либо 12 мая 2014 года, то вам не нужно говорить, что эта дата основывается на Григорианском календаре. 

Григорианский календарь был введен папой Григорием XIII в 1582. Boost.DateTime поддерживает календарные даты с 1400 до 9999 годов, что означает, что поддержка ведется с 1582 года. Таким образом вы можете использовать Boost.DateTime для любой календарной даты после 1400 года до тех пор, пока дата преобразуется в Григориансокм календаре. Дл обрабоки более ранней даты, Boost.DateTime должен быть переведен на новый календарь. 

Заголовочный файл `boost/date_time/gregorian/gregorian.hpp` содержит определения для всех классов и функций, которые обрабатывают календаные даты. Эти функции и классы могут быть найдены в пространстве имен `boost::gregorian`. Чтобы создать дату, используйте класс `boost::gregorian::date`. 

<a name="Example36.1"></a>
**Пример 36.1. Создание даты, используя `boost::gregorian::date`**
    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    int main()
    {
      boost::gregorian::date d{2014, 1, 31};
      std::cout << d.year() << '\n';
      std::cout << d.month() << '\n';
      std::cout << d.day() << '\n';
      std::cout << d.day_of_week() << '\n';
      std::cout << d.end_of_month() << '\n';
    } 
---
`boost::gregorian::date` предоставляет несколько конструкторов для создания даты. Самый основной конструктор принимает год, месяц и день в качестве параметров. Если задано недопустимое значение, будет выброшено исключение типа `boost::gregorian::bad_day_of_month`, `boost::gregorian::bad_year` или `boost::gregorian::bad_month`. Все эти классы будут производными от `std::out_of_range`. 

Как показано в [примере 36.1](#Example36.1) существует множество доступных членов-функций. Некоторые члены-функции, такие как `year()`, `month()` и `day()`, возвращают соответствующие компоненты даты и другие, такие как `day_of_week()` и `end_of_month()`, вычисляемые значения. 

Конструктор `boost::gregorian::date` ожидает числовые значения для года, месяца и дня, чтобы установить дату. Тем не менее, где **янв** - месяц, **пят** - день недели. Возвращаемые значения `month()` и `day_of_week()` являются не регулярными числовыми значениями, а значеними типа `boost::gregorian::date::month_type` и `boost::gregorian::date::day_of_week_type`. Boost.DateTime обеспечивает полную поддержку форматированного ввода и вывода, поэтому можно настроить выход, например, до **1** **Янв**.

Конструктор по умолчанию `boost::gregorian::date` создает новую дату. Недопустимая дата тоже может быть создана явным образом, путем передачи `boost::date_time::not_a_date_time` в качестве единственного параметра конструктору. 

Помимо вызова конструктора напрямую, объект типа `boost::gregorian::date` могут быть созданы с помощью свободно-стоящих функций и функций-членов других классов.

<a name="Example36.2"></a>
**Пример 36.2. Получение даты с часов или строки**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>
    
    using namespace boost::gregorian;

    int main()
    {
      date d = day_clock::universal_day();
      std::cout << d.year() << '\n';
      std::cout << d.month() << '\n';
      std::cout << d.day() << '\n';

      d = date_from_iso_string("20140131");
      std::cout << d.year() << '\n';
      std::cout << d.month() << '\n';
      std::cout << d.day() << '\n';
    }
    
[Пример 36.2](#Example36.2) использует класс `boost::gregorian::day_clock` который возвращает текущую дату. Функция-член `universal_day()` возвращает дату в формате UTC, который не зависит от часовых поясов и перехода на летнее время. UTC является международной абривиатурой для универсального времени. `boost::gregorian::day_clock` также предоставляет функцию-член `local_day()`, которая принимает локальные параметры во внимание. Чтобы получить текущую дату в местной временной зоне, используйте `local_day()`.

Пространство имен `boost::gregorian` содержит свободно-стоящие функции для преобразования даты, хранящуюся в формате строки в объект типа `boost::gregorian::date`. [Пример 36.2](Example36.2) преобразует дату в формате ISO 8601 используя функцию `boost::gregorian::date_from_iso_string()`. Другие функции ключают в себя: `boost::gregorian::from_simple_string()` и `boost::gregorian::from_us_string()`.

В то время как `boost::gregorian::date` обозначает определенное время, `boost::gregorian::date_duration` обозначает длительность. 

<a name="Example36.3"></a>
**Пример 36.3. Использование `boost::gregorian::date_duration`**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::gregorian;

    int main()
    {
      date d1{2014, 1, 31};
      date d2{2014, 2, 28};
      date_duration dd = d2 - d1;
      std::cout << dd.days() << '\n';
    }
    
Поскольку `boost::gregorian::date` перегружает `operator-` два момента времени могут вычтены друг из друга(смотри [Пример 36.3](#Example36.3)). Возвращаемое значение имеет тип `boost::gregorian::date_duration` и обозначает время между двумя датами.

Наиболее важной членом-функцией, предлагаемой `boost::gregorian::date_duration` является `days()`, которая возвращает количество дней в течении определенного промежутока времени.

<a name="Example36.4"></a>
**Пример 36.4. Специализированная продолжительность**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::gregorian;

    int main()
    {
      date_duration dd{4};
      std::cout << dd.days() << '\n';
      weeks ws{4};
      std::cout << ws.days() << '\n';
      months ms{4};
      std::cout << ms.number_of_months() << '\n';
      years ys{4};
      std::cout << ys.number_of_years() << '\n';
    }
    
Объекты типа `boost::gregorian::date_duration` также могут быть созданы путем передачи количества дней, как единственный параметр в конструктор. Чтобы создать продолжительность, которая включает несколько недель, месяцев или лет, следует использовать `boost::gregorian::weeks`, `boost::gregorian::months`, или `boost::gregorian::years`(смотри [Пример 36.4](#Example36.4))

Ни `boost::gregorian::months`, ни `boost::gregorian::years` позволяет определить количество дней, потому что месяцы и годы имеют разную длинну. Тем не менее, использование этих классов, как показано в [Примере 36.5](#Example36.5), все еще имеет смысл.

<a name="Example36.5"></a>
**Пример 36.5. Обработка специализированных продолжительностей**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::gregorian;

    int main()
    {
      date d{2014, 1, 31};
      months ms{1};
      date d2 = d + ms;
      std::cout << d2 << '\n';
      date d3 = d2 - ms;
      std::cout << d3 << '\n';
    }
    
[Пример 36.5](#Example36.5) добавляет один месяц на указанную дату: 31 января 2014, и **d2** становится 28 февраля 2014 года. В следующем шаге один месяц вычитается и **d3** опять становится 31 января 2014. Как показано, моменты времени, а так же время воздействия могут быть использованы в расчетах. Тем не менее, некоторые особенности должны быть приняты во внимание. Например, начиная с последнего дня месяца, `boost::gregorian::months` всегда возвращается в последний день другого месяца, что может привести к сюрпризам. 

<a name="Example36.6"></a>
**Пример 36.6. Сюрпризы при обработке специализированных длительностей**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::gregorian;

    int main()
    {
      date d{2014, 1, 30};
      months ms{1};
      date d2 = d + ms;
      std::cout << d2 << '\n';
      date d3 = d2 - ms;
      std::cout << d3 << '\n';
    }
    
[Пример 36.6](#Example36.6) идентичен предыдущему, за исключением того, что **d** инициализируется, как 30 января 2014. Несмотря на то, что это не последний день в январе, он прыгает на один месяц вперед, в результате **d2** становится 28 февраля 2014, потому что нет 30 февраля 2014. Тем не менее, снова прыаем на один месяц назад и **d3** становится равным 31 января 2014. С 28 февраля 2014, в последний день месяца прыгая назад возвращаемся в последний день января. 

Чтобы изменить такое поведение, сбрасываем макрос `BOOST_DATE_TIME_OPTIONAL_GREGORIAN_TYPES`. После того, как макрос становится неопределенным, классы `boost::gregorian::weeks`, `boost::gregorian::months`, и `boost::gregorian::years` больше не будут доступны. Единственный класс, который по прежнему будет доступен `boost::gregorian::date_duration` который просто прыгает вперед и назад на определенное число дней и не особое уделяет внимание на первый и последний день месяца. 

<a name="Example36.7"></a>
**Пример 36.7. Используем `boost::gregorian::date_period`**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::gregorian;

    int main()
    {
      date d1{2014, 1, 1};
      date d2{2014, 2, 28};
      date_period dp{d1, d2};
      date_duration dd = dp.length();
      std::cout << dd.days() << '\n';
    }
    
Пока `boost::gregorian::date_duration` работает только с продолжительностью, `boost::gregorian::date_period` поддерживает диапозоны между двумя датами. 

Конструктор `boost::gregorian::date_period` может принимать два вида ввода. Вы можете передать два параметра типа `boost::gregorian::date`, один для даты начала, другой для даты конца. Или же вы можете указать дату начала и длительность типа `boost::gregorian::date_duration`. Обратите внимание, что день перед датой окончания, фактически является последним днем периода. Это важно для понимания выхода в [Примере 36.8](#Example36.8)

<a name="Example36.8"></a>
**Пример 36.8. Тест, содержет ли период даты**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::gregorian;

    int main()
    {
      date d1{2014, 1, 1};
      date d2{2014, 2, 28};
      date_period dp{d1, d2};
      std::cout.setf(std::ios::boolalpha);
      std::cout << dp.contains(d1) << '\n';
      std::cout << dp.contains(d2) << '\n';
    }
    
[Пример 36.8](#Example36.8) проверяет, является ли конкретная дата в периоде, когда вызывается `contains()`. Обратите внимание на то, что хоть **d2** определяет конец периода, она не является частью периода. Таким образом, функция `contains()` возвращает true, когда вызывается **d1** и false вызывается **d2**.

`boost::gregorian::date_period` предоставялет дополнительные члены-функции для таких операций, как переключение периода и вычисление пересечения двух перекрывающихся периодов.

Boost.DateTime также предоставляет итераторы и другие, полезные, свободно-стоящие функции, как показано в [Примере 36.9](#Example36.9)

<a name="Example36.9"></a>
**Пример 36.9. Перебор дат**

    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost;

    int main()
    {
      gregorian::date d{2014, 5, 12};
      gregorian::day_iterator it{d};
      std::cout << *++it << '\n';
      std::cout << date_time::next_weekday(*it,
        gregorian::greg_weekday(date_time::Friday)) << '\n';
    }
    
С помощью итератора `boost::gregorian::day_iterator` можно прыгнуть вперед или назад на один день с определенной даты.Используйте `boost::gregorian::week_iterator`, `boost::gregorian::month_iterator`, и `boost::gregorian::year_iterator` чтобы прыгать на несколько недель, месяцев или лет, соответственно.

[Пример 36.9](#Example36.9) также использует функцию `boost::date_time::next_weekday()`, которая возвращает дату следующего дня недели на основе заданной даты. [Пример 36.9](#Example36.9) показывает **16 мая 2014**, что является первой пятницей после 13 мая 2014

---
<a name="LocationIndependentTimes"></a>
#Время независящее от места

Класс `boost::posix_time::ptime` определяет время независящее от места. Он использует тип `boost::gregorian::date`, но и сохраняет время. Чтобы использовать `boost::posix_time::ptime`, подключите заголовочный файл `boost/date_time/posix_time/posix_time.hpp`. 

<a name="Example36.10"></a>
**Пример 36.10. Использование `boost::posix_time::ptime`**

    #include <boost/date_time/posix_time/posix_time.hpp>
    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::posix_time;
    using namespace boost::gregorian;

    int main()
    {
      ptime pt{date{2014, 5, 12}, time_duration{12, 0, 0}};
      date d = pt.date();
      std::cout << d << '\n';
      time_duration td = pt.time_of_day();
      std::cout << td << '\n';
    }
    
Для инициализации объекта типа `boost::posix_time::ptime` передайте дату типа `boost::gregorian::date` и длительность типа `boost::posix_time::time_duration` в качестве первого и второго параметра в конструкторе. Конструктор `boost::posix_time::time_duration` принимает три параметра, которые определяют время. [Пример 36.10](#Example36.10) определяет 12 часов 12 мая 2014 года, как момент времени. Для запроса даты и времени, используйте члены-функции `date()` и `time_of_day()`.

Подобно тому, как конструктор по умолчанию `boost::gregorian::date` создает некорректную дату, конструктор по умолчанию boost::posix_time::ptime создает некорректное время. Некорректное время также может быть создано явно, встявляя `boost::date_time::not_a_date_time` в конструктор. Boost.DateTime заставляет свободно-стоящие функции и функции-члены создавать время аналогично тому, как создаются календарные даты типа `boost::gregorian::date`. 

<a name="Example36.11"></a>
**Пример 36.11. Создание временной шкалы с использованием часов или строки**

    #include <boost/date_time/posix_time/posix_time.hpp>
    #include <boost/date_time/gregorian/gregorian.hpp>
    #include <iostream>

    using namespace boost::posix_time;

    int main()
    {
      ptime pt = second_clock::universal_time();
      std::cout << pt.date() << '\n';
      std::cout << pt.time_of_day() << '\n';

      pt = from_iso_string("20140512T120000");
      std::cout << pt.date() << '\n';
      std::cout << pt.time_of_day() << '\n';
    }
    
Класс `boost::posix_time::second_clock` возвращает текущее время. Функция-член `universal_time()` возвращает время в формате UTC (смотри [Пример 36.11](#Example36.11)). `local_time()` возвращает локальное время. Если вам необходим более точный результат `boost::posix_time::microsec_clock` возвращает текущее время, включая микросекунды.

Свободно-стоящая функция `boost::posix_time::from_iso_string()` преобразует время хранящееся в формате строки, отформатированное с использованием стандарта ISO 8601, в объект типа `boost::posix_time::ptime`.

<a name="Example36.12"></a>
**Пример 36.12. Использование `boost::posix_time::time_duration`**

    #include <boost/date_time/posix_time/posix_time.hpp>
    #include <iostream>

    using namespace boost::posix_time;

    int main()
    {
      time_duration td{16, 30, 0};
      std::cout << td.hours() << '\n';
      std::cout << td.minutes() << '\n';
      std::cout << td.seconds() << '\n';
      std::cout << td.total_seconds() << '\n';
    }
    
Boost.DateTime также предоставляет класс `boost::posix_time::time_duration`, который определяет продолжительность. Этот класс уже упоминался ранее, так как конструктор `boost::posix_time::ptime` ожидает объект типа `boost::posix_time::time_duration`, как второй параметр. Вы также можете использовать `boost::posix_time::time_duration` независимо друг от друга.

`hours()`, `minutes()`, и `seconds()` возвращает соответствующие части продолжительности времени, в то время, как функции-члены, такие как `total_seconds()`, которые возвращают общее количество секунд, предоставляют дополнительную информацию(смотри [Пример 36.12](#Example36.12)). Здесь нет верхнего предела, например 24 часа, значения вы можете перешагнуть используя `boost::posix_time::time_duration`. 

<a name="Example36.13"></a>
**Пример 36.13. Обработка моментов времени**

    #include <boost/date_time/posix_time/posix_time.hpp>
    #include <iostream>

    using namespace boost::posix_time;
    using namespace boost::gregorian;

    int main()
    {
      ptime pt1{date{2014, 5, 12}, time_duration{12, 0, 0}};
      ptime pt2{date{2014, 5, 12}, time_duration{18, 30, 0}};
      time_duration td = pt2 - pt1;
      std::cout << td.hours() << '\n';
      std::cout << td.minutes() << '\n';
      std::cout << td.seconds() << '\n';
    }
    
Как и в случае календарных дат, расчеты могут быть выполнены с моментом времени и продолжительностью. Если два времени, типа `boost::posix_time::ptime` вычитаются друг от друга, как описано в [Примере 36.13](#Example36.13), результататом является объект типа `boost::posix_time::time_duration` определяющий длительность между двумя временами.

<a name="Example36.14"></a>
**Пример 36.14. Обработка длительности**

    #include <boost/date_time/posix_time/posix_time.hpp>
    #include <iostream>

    using namespace boost::posix_time;
    using namespace boost::gregorian;

    int main()
    {
      ptime pt1{date{2014, 5, 12}, time_duration{12, 0, 0}};
      time_duration td{6, 30, 0};
      ptime pt2 = pt1 + td;
      std::cout << pt2.time_of_day() << '\n';
    }
    
Как показано в [Примере 36.14](#Example36.14), длительность может быть добавлена к времени, в результате у нас поулчается новый момент времени. В этом примере мы вписываем **18:30:00** в стандартный поток.

Boost.DateTime использует одни и те же понятия для календарных дат и времени. Так же, как есть классы для времени и длительности, есть и один для периодов. Для календарных дат, это `boost::posix_time::time_period`; для времени это `boost::posix_time::time_period`. Конструкторы обоих классов ожидают два параметра: `boost::gregorian::date_period` ожидает две календарные даты в качестве параметров и `boost::posix_time::time_period` ожидает два момента времени. 

<a name="Example36.15"></a>
**Пример 36.15. Использование `boost::posix_time::time_period`**

    #include <boost/date_time/posix_time/posix_time.hpp>
    #include <iostream>

    using namespace boost::posix_time;
    using namespace boost::gregorian;

    int main()
    {
      ptime pt1{date{2014, 5, 12}, time_duration{12, 0, 0}};
      ptime pt2{date{2014, 5, 12}, time_duration{18, 30, 0}};
      time_period tp{pt1, pt2};
      std::cout.setf(std::ios::boolalpha);
      std::cout << tp.contains(pt1) << '\n';
      std::cout << tp.contains(pt2) << '\n';
    }
    
В целом, `boost::posix_time::time_period` работает в точности, как `boost::gregorian::date_period`. Это обеспечивает функция-член, `contains()`, которая возвращает true для каждого момента времени в течение периода. Потому что время окончания, которое передается в конструктор `boost::posix_time::time_period` не является частью периода, повторный вызов `contains()`, в [Примере 36.15](#Example36.15) возвращает false.

`boost::posix_time::time_period` предоставляет дополнительные функции-члены, такие как `intersection()` и `merge()` которые соответственно, вычислют пересечение двух перекрывающихся периодов и объединяют два пересекающихся периода.

Наконец, итератор `boost::posix_time::time_iterator` перебирает моменты времени. 

<a name="Example36.16"></a>
**Пример 36.16. Перебор моментов времени**

    #include <boost/date_time/local_time/local_time.hpp>
    #include <iostream>

    using namespace boost::posix_time;
    using namespace boost::gregorian;

    int main()
    {
      ptime pt{date{2014, 5, 12}, time_duration{12, 0, 0}};
      time_iterator it{pt, time_duration{6, 30, 0}};
      std::cout << *++it << '\n';
      std::cout << *++it << '\n';
    }
    
[Пример 36.16](#Example36.16) использует итератор прыжка вперед на на 6.5 часов от времени после полудня. Поскольку итератор увеличивается в два раза, на выходе имеем **2014 май 12 18:30:00** и **2014 май 12 1:00:00**

---
