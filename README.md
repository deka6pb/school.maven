Maven vol.2

Apache Maven - это инструмент управления проектами. Основан на концепции объектной модели проекта (POM). Maven может управлять сборкой, публикацией и документацией проекта.

# Project Object Model (POM)

/src/main/java - исходный код
/src/main/resources - Ресурсы
/src/test - тесты
/target - Дистрибутив JAR
/target/classes - Скомпилированный байт-код

POM означает «Объектная модель проекта». Это XML-представление проекта Maven, хранящегося в файле с именем pom.xml

Что представляет из себя POM файл:
https://maven.apache.org/pom.html


Все POM-файлы являются наследниками родительского POM. Этот POM-файл называется Super POM и содержит значения, унаследованные по умолчанию.
https://maven.apache.org/ref/3.0.4/maven-model-builder/super-pom.html

```
mvn help:effective-pom
```

# Жизненный цикл сборки:

Существует 3 жизненных цикла:
clean
default
site

Каждая фаза имеет pre и post фазы. Их можно также использовать для регистрации задач.

Когда Maven начинает сборку проекта, он проходит через определённую последовательность фаз и выполняет определённые задачи, которые указаны в каждой из фаз. Maven имеет следующие 3 стандартных жизненных цикла:

Задача (goal) – это специальная задача, которая относится к сборке проекта и его управлению. Она может привязываться как к нескольким фазам, так и ни к одной. Задача, которая не привязана ни к одной фазе, может быть запущена вне фаз сборки с помощью прямого вызова.

mvn clean dependency:copy-dependencies package
- фаза clean
- задача dependency:copy-dependencies
- фаза package 

# Жизненный цикл clean:
состоит из 3 фаз:
- pre-clean
- clean
- post-clean

```
cp lifecycle/clean.pom.xml pom.xml
mvn clean
mvn clean:clean
```
Используется плагин clean. В нем присутствует задача (goal) clean:clean которая привязана к фазе clean. Поэтому когда мы пишем mvn clean - мы вызываем фазу clean. А уже с ней вызывается привязанная к ней задача clean:clean.
То есть каждый плагин всего лишь поставляет задачи, привязанные к определенным фазам.


# Жизненный цикл Default (Build):
Основные из них:

validate — в этой фазе проверяется корректность проекта и обеспечивается доступность необходимых зависимостей
compile — Компилируется исходный код проекта
test — Код тестов компилируется и запускаются unit тесты
package — Скомпилированный код собирается в пакет (jar/war/ear/etc)
verify — Запускаются интеграционные тесты
install — Собранный ранее пакет устанавливается в локальный репозиторий и становится доступен для сборки других локальных проектов
deploy — Пакет публикуется в удалённых репозиториях, устанавливается на серверы приложений и так далее

```
cp lifecycle/default.pom.xml pom.xml
mvn compile
```
Для тех кому интересно 23 фазы: https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html

Вызывать вручную можно все фазы, но, обычно, фазы с ‘-‘ в названии не принято вызывать явно, так как они могут оставить сборку в неоконченном виде. Например, вызвав integration-test вместо verify вы останетесь с неубранным мусором от интеграционных тестов.

# Жизненный цикл Site:
Включает 4 фазы:
- pre-site
- site
- post-site
- site-deploy
```
cp lifecycle/site.pom.xml pom.xml
mvn site
```
https://maven.apache.org/doxia/


# Плагины

В этом фреймворке каждая задача, по сути своей, выполняется с помощью плагинов.

Так как плагины являются такими же артефактами, как и зависимости, то они описываются практически так же. Вместо раздела dependencies – plugins, dependency – plugin, repositories – pluginRepositories, repository – pluginRepository.

Плагины Maven использутся для:

создания jar – файла
создания war – файла
компиляции кода файлов
юнит-тестирования кода
создание отчётов проекта
создание документации проекта

mvn [имя-плагина]:[имя-цели]

mvn compiler:compile

2 типа плагинов:
- Плагины сборки - Выполняются в процессе сборки и должны быть конфигурированны внутри блока <build></build> файла pom.xml
- Плагины отчётов - Выполняются в процесса генерирования сайта и должны быть конфигурированны внутри блока <reporting></reporting> файла pom.xml

clean - Очищает цель после сборки. Удаляет директорию target.

compiler - Компилирует исходные Java файлы.

surefire - Запускает тесты JUnit. Создаёт отчёты о тестировании.

failsafe - Запускает интеграционные тесты. Создаёт отчёты о тестировании.

jar - Собирает JAR файл текущего проекта.

war - Собирает WAR файл текущего проекта.

javadoc - Генерирует Javadoc проекта.

antrun - Запускает набор задач ant из любой указанной фазы.

```
cp plugins/complie.pom.xml pom.xml
mvn compile
```

http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Built-in_Lifecycle_Bindings

- Плагины указываются в файле pom.xml внутри блока <plugins></plugins>
- Каждый плагин может иметь несколько целей.
- Мы можем определять фазу, из которой мы можем начать выполнение плагина. В примере выше мы использовали фазу compile.


# Профили
pom.xml - для проекта
%USER_HOME%/.m2/settings.xml - для пользователя
%M2_HOME%/conf/settings.xml - глобальные настройки

Активировать профили можно:
- Использованием команды в консоли
- С помощью настроек Maven
- С помощью переменных окружения
- Настройках ОС
- Существующими, отсутствующими файлами.

Из командной строки:
```
cp profiles/test.environment.pom.xml pom.xml
mvn clean test -Ptest
mvn clean test -Pnormal
```

Из settings.xml
```
cp profiles/settings.xml ~/.m2/settings.xml
mvn test
```

Из pom.xml
```
rm ~/.m2/settings.xml
cp profiles/pom.activation.profile.pom.xml pom.xml
```
Можно посомтреть полный pom.xml
mvn help:effective-pom -P test


# settings.xml
обычно в нем хранят: 
- локальное расположение репозитория
- альтернативные серверы удаленных репозиториев
- данные аутентификации.

Есть два файла, в которых может находиться файл settings.xml :
${maven.home}/conf/settings.xml - глобальный
${user.home}/.m2/settings.xml - пользовательский

Если оба файла существуют, их содержимое объединяется, а пользовательские settings.xml являются доминирующими.
```
cp settings/simple.settings.xml ~/.m2/settings.xml
less ~/.m2/settings.xml
```
Полный перечень settings:
https://maven.apache.org/settings.html

localRepository - путь к локальному репозиторию этой системы сборки.

interactiveMode : true, если Maven должен пытаться взаимодействовать с пользователем для ввода, false, если нет. Значение по умолчанию равно true.

usePluginRegistry : true, если Maven должен использовать файл $ {user.home}/.m2/plugin-registry.xml для управления версиями плагина, по умолчанию используется значение false. Обратите внимание, что для текущей версии Maven 2.0 не следует зависеть от файла plugin-registry.xml.

offline: true, если эта система сборки должна работать в автономном режиме, значение по умолчанию - false . Этот элемент полезен для серверов сборки, которые не могут подключиться к удаленному репозиторию, либо из-за настройки сети, либо из соображений безопасности.

pluginGroups
Вместо: org.mortbay.jetty:jetty-maven-plugin:run
Можно выполнить: mvn jetty:run


Элемент profile в файле settings.xml представляет собой усеченную версию элемента профиля pom.xml. 
Состоит из элементов:
- activation
- repositories
- pluginRepositories
- properties

Приоритет для профилей:
1. Глобальный
2. Пользовательский
3. Проектный

Если профиль активен из settings.xml, его значения будут переопределять любые эквивалентные ID'-профили в файле pom.xml.

Для mirrors:
- пользователские
- глобальный

Для repositories:
- pom.xml
- Локальный
- Глобальный

Так выглядит настройка удаленного репозитория:
```
cp repositories/remote.pom.xml pom.xml
```
```
less repositories/snapshot.pom.xml pom.xml
```

# Транзитивные зависимости

Появились с версии Maven 2.0

Проект -> A -> B -> C (2.5)
Проект -> D -> C (2.1)

По дефолту будет выбрана версия SNAPSHOT 2.1, потому что граф короче.
Если мы хотим явно указать, то это можно сделать в:
```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com</groupId>
            <artifactId>C</artifactId>
            <version>2.5</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

https://www.youtube.com/watch?v=LbhFO3im6vo

```
cp dependencies/external.pom.xml pom.xml
mvn package
```
dependencyManagement

Parent:
```
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
Child:
```
<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
  </dependency>
</dependencies>
```
# Scope
Scope позволяет указать maven’у когда и для чего вам нужна эта зависимость. Всего существует шесть областей видимости:

compile — область видимости по умолчанию. Зависимости с этим scope будут доступны и во время сборки и во время тестирования и их даже добавят в конечный пакет, чтобы они были доступны и во время исполнения. Более того, maven распространит их дальше и сделает доступными в зависимых пакетах.

provided — Почти как compile, но в пакет зависимость добавлена не будет. Предполагается что данные библиотеки будут предоставлены средой выполнения, например J2EE контейнером. Каноничный пример такой зависимости — J2EE API, конкретная реализация которых предоставляется контейнером J2EE. Примеры:  hibernate или jsf.

runtime — антипод provided. Означает зависимость, которая требуется для исполнения/тестирования кода, но не для его сборки. Зависимости из этого
scope так же будут добавлены в пакет.

test — зависимости, которые нужны только и исключительно для тестов. Как JUnit из примера выше.

system — зависимость которая присутствует в среде Java всегда, тем или иным путём. Maven не будет пытаться предоставить этот артефакт или класть
его в пакет итд.

import — использутся для импорта зависимостей из других артефактов и управлением зависимостями в сложных пакетах, состоящих из нескольких артефактов.

```
cp scope/scope.pom.xml pom.xml
```

org.projectlombok:lombok — утилита для генерации кода, работающая только во время компиляции. Поэтому у неё scope — provided

org.slf4j:slf4j-api — Фронтенд для ведения логов. Мой проект с ним собирается, тестируется и работает. Scope Slf4j — compile.

org.slf4j:slf4j-log4j12 — Реализация Slf4j поверх log4j. Мой проект не ссылается напрямую на эту библиотеку и использует её только посредством Slf4j, поэтому её Scope — runtime.

junit:junit — Фреймворк юнит-тестирования. Очевидно что он нужен только во время исполнения тестов и из-за этого его scope — test.


# Исключение зависимостей
```
<dependency>
  <groupId>org.dbunit</groupId>
  <artifactId>dbunit</artifactId>
  <version>${dbunit.version}</version>
  <scope>test</scope>
</dependency>
<dependency>
  <groupId>org.unitils</groupId>
  <artifactId>unitils-dbmaintainer</artifactId>
  <version>${unitils.version}</version>
  <scope>test</scope>
  <exclusions>
    <exclusion>
      <groupId>org.dbunit</groupId>
      <artifactId>dbunit</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```
Я знаю, что unitils-dbmaintainer зависит от dbunit предыдущей версии, но тесты моего приложения зависят от более новой версии. Поэтому я явно добавляю зависимость от dbunit нужной мне версии и исключаю старый dbunit из зависимостей unitils-dbmaintainer.

# Snapshot

Snapshot – это специальная версия, которая показывает текущую рабочую копию. При каждой сборке Maven проверяет наличие новой snapshot версии на удалённом репозитории.

В случае с версией, если Maven однажды загрузил версию business-logic:1.0, то он больше не будет пытаться загрузить новую версию 1.0 из репозитория. Для того, чтобы скачать обновлённый продукт business-logic должен быть обновлён до версии 1.1.

В случае со snapshot, Maven автоматически будет подтягивать крайний snapshot (business-logic:1.0-SNAPSHOT) каждый раз, когда команда будет выполнять сборку своего проекта.

mvn clean package -U помогает подтягивать изменения не из кэша


# Автоматизация развёртывания

```
cp deployment/deploy.pom.xml pom.xml
```

Repositories - Место, где выполняется сборка JAR, WAR, или EAR или другие артифакты, которые будут получены в случае успешной сборки.
Plugin - Элемент maven-release-plugin сконфигурирован для автоматизации процесса развёртывания.
SCM - Определяет расположение системы контроля версий, в которой Maven выполнит проверку исходного кода.

Команды:
mvn release:clean - Выполняет “очистку” в случае, если релиз не был успешным.
mvn release:rollback - “Откатывает изменения”, которые были сделаны в коде и конфигурациях, если крайний релиз не был успешным.
mvn release:prepare - 
- Проверяет наличие изменений, которые не были внесены в системы контроля версий;
- Проверяет, что нет snapshot зависимостей;
- Обновляет pom.xml файл в системе контроля версий;
- Проверяет версию приложения и удаляет snapshot-ы из версии перед релизом;
- Выполняет тесты;
- Выполняет коммит изменённого файла pom.xml;
- Отмечает код в системе контроля версий;
- Изменяет (увеличивает) номер версии и “подтягивает” snapshot-ы для будущих релизов;
- Выполняет коммит изменённых файлов pom.xml в системы контроля версий.


Литература:
https://maven.apache.org/
https://easyjava.ru/ecosystem/buildtools/maven
https://proselyte.net/tutorials/maven/


## Вопрос/Ответ на лекции:
- Почему не вызывался post-clean? У жизненного цикла есть 3 фазы: pre-clean, clean, post-clean. Если мы вызовем mvn clean, то пройдем всего 2 фазы из трех. Если вызовем mvn post-clean, то вызовем все 3 фазы и пройдем весь жизненный цикл. pre и post фазы присутствуют только в жизненном цикле clean. Они не связаны с фазой clean. Это 3 равноценные фазы, как допустим в Default жизненном цикле validate и compile.
- Что за фаза site-deploy? если вы хотите сгенерировать сайт и развернуть его за один раз, вы можете использовать фазу site-deploy для жизненного цикла сайта. Для этого просто выполните: mvn site-deploy. Более подробно можно почитать здесь: https://maven.apache.org/plugins/maven-site-plugin/usage.html
- Какие могут быть goal? Goal run встроен в maven? Goal поставляет нам плагин. Goal run присутствует в плагине maven-antrun-plugin. Еще примеры:
```
<plugin>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-maven-plugin</artifactId>
    <version>3.4.1</version>
    <configuration>
        <!-- put the configuration here that is common to all executions -->
    </configuration>
    <executions>
        <execution>
            <id>diff</id>
            <goals>
                <goal>diff</goal>
            </goals>
            <configuration>
                <!-- put the specific configuration of the diff goal here, this will inherit from the global configuration -->
            </configuration>
        </execution>
        <execution>
            <id>update</id>
            <goals>
                <goal>update</goal>
            </goals>
            <configuration>
                <!-- put the specific configuration of the update goal here, this will inherit from the global configuration -->
            </configuration>
        </execution>
    </executions>
</plugin>
```
- Можно ли установить scope provided для какой-либо кастомной библиотеки? Судя по всему нет. Только Jdk и Web container.
- Приоритет profile?
    1) System properties: set with -Dxyz=value on the command line.
    2) From currently active profile(s): settings.xml in user home directory first, then profiles.xml in project root directory, then in profiles defined in your pom.xml.If many profiles are active, and a property is defined in more than one of those, the order of precedence is based on the last profile in which this property is defined, in alphabetical order of profile name.
    3) In the properties section of your pom.xml.
    4) Lastly, in properties defined in filters. If a property is defined in multiple filters, then the last one (in order of appearance in your filters section) has precedence over the others.