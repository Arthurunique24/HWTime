#Глава 36. Boost.DateTime
#### Содержание 
[Календарные даты](#CalendarDates)

[Время независящее от места](https://theboostcpplibraries.com/boost.datetime-location-independent-times)

[Время зависязее от места](https://theboostcpplibraries.com/boost.datetime-location-dependent-times)

[Форматированный ввод и вывод](https://theboostcpplibraries.com/boost.datetime-formatted-input-and-output)

Библиотека [Boost.DateTime](http://www.boost.org/doc/libs/1_62_0/doc/html/date_time.html) может быть использована для обработки временных данных, таких как календарные даты и время. Кроме того, Boost.DateTime предоставляет расширения для ля учета часовых поясов и поддерживает форматированный ввод и вывод календарных дат и времени. Если вы ищете функции для получения текущего времени или измерения текущего времени, смотри Boost.Chrono в [Главе 37](https://theboostcpplibraries.com/boost.chrono)

<a name="CalendarDates"></a>
#Календарные даты 
Boost.DateTime поддерживает только кадендарные даты на основе Григорианского календаря, который совершенно не является проблемой, так как это наиболее это наиболее широко используемый календарь. Если вы организовываете встречу с кем-либо 12 мая 2014 года, то вам не нужно говорить, что эта дата основывается на Григорианском календаре. 

Григорианский календарь был введен папой Григорием XIII в 1582. Boost.DateTime поддерживает календарные даты с 1400 до 9999 годов, что означает, что поддержка ведется с 1582 года. Таким образом вы можете использовать Boost.DateTime для любой календарной даты после 1400 года до тех пор, пока дата преобразуется в Григориансокм календаре. Дл обрабоки более ранней даты, Boost.DateTime должен быть переведен на новый календарь. 

Заголовочный файл `boost/date_time/gregorian/gregorian.hpp` содержит определения для всех классов и функций, которые обрабатывают календаные даты. Эти функции и классы могут быть найдены в пространстве имен `boost::gregorian`. Чтобы создать дату, используйте класс `boost::gregorian::date`. 

<a name="example"></a>
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

Как показано в [примере 36.1](#example) 
