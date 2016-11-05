#Глава 36. Boost.DateTime
#### Содержание 
[Календарные даты](#CalendarDates)

[Время независящее от места](#LocationIndependentTimes)

[Время зависязее от места](#LocationDependentTimes)

[Форматированный ввод и вывод](#FormattedInputAndOutput)

Библиотека [Boost.DateTime](http://www.boost.org/doc/libs/1_62_0/doc/html/date_time.html) может быть использована для обработки временных данных, таких как календарные даты и время. Кроме того, Boost.DateTime предоставляет расширения для ля учета часовых поясов и поддерживает форматированный ввод и вывод календарных дат и времени. Если вы ищете функции для получения текущего времени или измерения текущего времени, смотри Boost.Chrono в [Главе 37](https://theboostcpplibraries.com/boost.chrono)

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

Как показано в [примере 36.1](#Example) существует множество доступных членов-функций. Некоторые члены-функции, такие как `year()`, `month()` и `day()`, возвращают соответствующие компоненты даты и другие, такие как `day_of_week()` и `end_of_month()`, вычисляемые значения. 

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
    
[Пример 36.2](Example36.2) использует класс `boost::gregorian::day_clock` который возвращает текущую дату. Функция-член `universal_day()` возвращает дату в формате UTC, который не зависит от часовых поясов и перехода на летнее время. UTC является международной абривиатурой для универсального времени. `boost::gregorian::day_clock` также предоставляет функцию-член `local_day()`, которая принимает локальные параметры во внимание. Чтобы получить текущую дату в местной временной зоне, используйте `local_day()`.

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
    
Поскольку `boost::gregorian::date` перегружает `operator-` два момента времени могут вычтены друг из друга(смотри [Пример 36.3](Example36.3)). Возвращаемое значение имеет тип `boost::gregorian::date_duration` и обозначает время между двумя датами.

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
    
Объекты типа `boost::gregorian::date_duration` также могут быть созданы путем передачи количества дней, как единственный параметр в конструктор. Чтобы создать продолжительность, которая включает несколько недель, месяцев или лет, следует использовать `boost::gregorian::weeks`, `boost::gregorian::months`, или `boost::gregorian::years`(смотри [Пример 36.4](Example36.4))

Ни `boost::gregorian::months`, ни `boost::gregorian::years` позволяет определить количество дней, потому что месяцы и годы имеют разную длинну. Тем не менее, использование этих классов, как показано в [Примере 36.5](Example36.5), все еще имеет смысл.

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
    
[Пример 36.5](Example36.5) добавляет один месяц на указанную дату: 31 января 2014, и **d2** становится 28 февраля 2014 года. В следующем шаге один месяц вычитается и **d3** опять становится 31 января 2014. Как показано, моменты времени, а так же время воздействия могут быть использованы в расчетах. Тем не менее, некоторые особенности должны быть приняты во внимание. Например, начиная с последнего дня месяца, `boost::gregorian::months` всегда возвращается в последний день другого месяца, что может привести к сюрпризам. 

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
    
[Пример 36.6](Example36.6) идентичен предыдущему, за исключением того, что **d** инициализирующегося, как 30 января 2014.
