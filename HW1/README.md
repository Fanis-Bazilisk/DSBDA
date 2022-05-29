##Задача

Программа, которая строит тепловую карту кликов по странице Входные данные: координаты нажатия x,y, userId, timestamp (справочник областей экрана: <координаты области> - <название области>, справочник температур: <диапазон значений> - <температура (высокая, средняя, низкая)>) Выходные данные: название области экрана, количество нажатий (температура)


№№Output format

SequenceFile со Snappy сжатием (плюс команда просмотра содержимого сжатого файла посредством распаковки). Приложить скриншот просмотра сжатого контента.

## Tests

Тесты были написаны на основное поведение программы - проверка работы Map, Reduce и MapReduce стадий. Написаны тесты на работу счетчиков. В качестве счетчиков применялось два значения - для испорченных записей MALFORMED и для общего количества задействованных секторов (для полного заполнения при разрешении 1920x1080 = 144)

(img/Снимок.png)

## HDFS

Скриншоты из веб-интерфейса hadoop:

JSON с координатами секторов

(img/Снимок2.png)

JSON с распределением по температуре (количество кликов)

(img/Снимок3.png)

Пример входного файла

(img/Снимок4.png)

## MapReduce Job

Приводятся старт и конец:

(img/Снимок5.png)
(img/Снимок6.png)

## Build && Deploy

**Требования к ПО и устройству**

        - Java 8
        - Maven
        - Python 3
        - Hadoop 3.3.3

В данной программе используется система сборки Maven
Для создания исполняемого пакета запускаем в `./HW1`:

```sh
mvn package
```

После успешной сборки пакета и прохождения тестов в директории `target/` появится файл `HW1-1.0-SNAPSHOT-jar-with-dependencies.jar` – пакет со всеми зависимостями

Для генерации файлов был сформирован код (не без помощи stackoferflow) на python:

```sh
python3 generator.py --help
usage: generator.py [-h] [-c C] [-x X] [-y Y] [-s S] [-real] [-live]
                    [-users USERS]

Generator of clicks on display

options:
  -h, --help    show this help message and exit
  -c C          amount of log strings
  -x X          width of display
  -y Y          height of display
  -s S          size of file
  -real         use real generate (too long)
  -live         use real mouse
  -users USERS  count of users
```

По умолчанию выставлен размер экрана в 1920х1080. На выходе создается директория `input/` с тремя файлами для входных значений.

Пример запуска в процессе эксперимента:

```sh
python3 generator.py -s 100K -real
```

Для запуска в hadoop загрузим в **HDFS** сгенерированные данные

```sh
hdfs dfs -put SECTORS SECTORS
hdfs dfs -put TEMPERATURE TEMPERATURE
hdfs dfs -put input/ input
```

Для удаления используется команда:

```sh
hdfs dfs -rm -r SECTORS
hdfs dfs -rm -r TEMPERATURE
hdfs dfs -rm -r input
hdfs dfs -rm -r output
```

Для запуска в псевдораспределенном режиме используется `yarn`:

```sh
yarn jar target/HW1-1.0-SNAPSHOT-jar-with-dependencies.jar input output SECTORS TEMPERATURE
```

Просмотр сжатого файла результата (SequenceFile) можно осуществить средствами `hdfs`:

```sh
hdfs dfs -text output/p*
```

(img/Снимок7.png)
