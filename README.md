Домашнее задание к занятию «Выстраивание процесса непрерывной интеграции»
Инструкция к выполнению домашнего задания
Перед тем как отправить своё решение на проверку преподавателю, сверьтесь с чеклистом.

1. В решении выполнены все требования задания
2. Правильно настроен Maven-проект, тесты проходят
3. Что делать, если возникли сложности
4. Отформатирован код [⚠️: в этом задании форматировать нужно только код тестов]
5. Настроен Github CI с verify-сборкой Maven и JaCoCo 🆕
CI
После связывания локального репозитория с удалённым и первого пуша в заготовки проекта, время настроить CI на основе GitHub Actions. Шаблон вашего maven.yml должен выглядеть вот так, убедитесь, что всё совпадает с вашим шаблоном, например, что вы указали фазу verify, а не package:

name: Java CI with Maven

on: [push, pull_request]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
    - name: Build with Maven
      run: mvn -B -e verify
JaCoCo
            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>0.8.5</version>
                ...
Инициализация:

                    <execution>
                        <id>prepare-agent</id>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>
В режиме генерации отчётов:

                    <execution>
                        <id>report</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>
В режиме проверки и обрушения сборки по уровню покрытия:

                    <execution>
                        <id>check</id>
                        <goals>
                            <goal>check</goal>
                        </goals>
                        <configuration>
                            <rules>
                                <rule>
                                    <limits>
                                        <limit>
                                            <counter>LINE</counter>
                                            <value>COVEREDRATIO</value>
                                            <minimum>100%</minimum>
                                        </limit>
                                    </limits>
                                </rule>
                            </rules>
                        </configuration>
                    </execution>
Задание 1. Синдром 100% (обязательное к выполнению)
Вы попали в команду максималистов, которые хотят, чтобы те автотесты, которые вы пишете, покрывали код на 100%.

Но вот незадача:

непонятно, что такое 100%;
непонятно, как это сделать.
Вспоминаем: покрытием кода у нас занимается JaCoCo, но он просто сигнализирует о том, что конкретно пошло не так.

Большинство подобных плагинов, помимо целей отчётности (report), содержат ещё цель check, которая обрушает сборку, если не выполнены определённые проверки.

Что вам нужно

Создать Мавен-проект с тестируемым кодом из листинга кода, он указан ниже по условию.
Изучить документацию на плагин, а конкретно — на цель check.
Внедрить эту цель во фазу verify. Обратите внимание, что эта цель и так публикуется в эту фазу.
Настроить правила по покрытию на 100%. При этом нужно изучить разницу между счётчиками INSTRUCTION, LINE, BRANCH, COMPLEXITY.
Вникнуть в тестируемый код.
Выбрать один из счётчиков и добиться 100% покрытия через добавление новых тестов.
Важно: использовать можно только один из следующих:

INSTRUCTION
LINE
BRANCH
Обратите внимание на чеклист в начале условия, он содержит подсказки по внедрению JaCoCo в ваш Мавен-проект.

Тестируемый код, его как-либо редактировать нельзя:

package ru.netology.statistic;

public class StatisticsService {
  /**
   * Calculate index of max income
   *
   * @param incomes - array of incomes
   * @return - index of first max value
   */
  public long findMax(long[] incomes) {
    long current_max_index = 0;
    long current_max = incomes[0];
    for (long income : incomes)
      if (current_max < income)
        current_max = income;
        return current_max;
  }
}
Класс с тестами, его надо будет расширить новыми тестами:

package ru.netology.statistic;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class StatisticsServiceTest {

  @Test
  void findMax() {
    StatisticsService service = new StatisticsService();

    long[] incomesInBillions = {12, 5, 8, 4, 5, 3, 8, 6, 11, 11, 12};
    long expected = 12;

    long actual = service.findMax(incomesInBillions);

    assertEquals(expected, actual);
  }
}
Итого: отправьте на проверку ссылку на репозиторий GitHub с вашим проектом.

Задание 2*. Пусть плагин ищет баги (необязательная задача)
SpotBugs и Maven Plugin для него предоставляют возможность производить статический анализ, то есть анализ кода без его запуска, для выявления наиболее часто встречающихся багов.

Список багов, которые ищет SpotBugs.

Ваша задача

Подключить плагин к вашему проекту. Возьмите проект из первой задачи или создайте новый на его базе.
Настроить goal check в фазу verify.
Удостовериться, что код проходит проверки SpotBugs, если не проходит, то пофиксить.
Сделать push в GitHub и удостовериться, что сборка проходит.
Итого: отправьте на проверку ссылку на репозиторий GitHub с вашим проектом.# dz8.1jv
