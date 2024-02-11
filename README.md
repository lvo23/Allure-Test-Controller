[eng](./README-en.md)

# Allure Test Controller


## Обзор
Этот Apache JMeter плагин представляет контроллер, который содержит в себе один или несколько тест-кейсов, результаты выполнения которых будут представлены в совместимом с Allure формате.

## Мотивация
JMeter имеет встроенный гибкий механизм генерации отчётов тестирования. Но его базовых возможностей не хватает для работы с фреймворком Allure Report и TMS, использующими этот инструмент для визуализации отчётов. Данный плагин призван решить эту проблему и предоставить возможность настраивать результаты тестирования под свои нужны в удобном для пользователя интерфейсе и сохранять их в правильном формате.

## Описании атрибутов контроллера и способ их применения:
- **Results folder path**: Указание пути к папке с результатами тестирования. Единственное обязательное для заполнения поле. Для указания пути можно воспользоваться кнопкой "Browse...". ВНИМАНИЕ! Не указывайте здесь конечную папку, а только путь к ней. В указанной директории будет создана папка "allure-results", в которую и будут попадать результаты тестов.
  
- **Overwrite folder**: Если установлен, то папка, указанная по пути из "Results folder path" будет очищена перед выполнением контроллера. Рекомендуется использовать только для первого контроллера внутри Thread Group, если вы используете внутри сценария только одну группу потоков. Если вы используете несколько групп потоков, то использовать чек-бокс следует только перед началом тестирования. Например, внутри SetUp Thread Group.
  
- **Stop test on error**: Если установлен, то останавливает поток, если внутри контроллера любой из сэмплеров выполняется с ошибкой.
  
- **Single step tests**: Если установлен, то изменяет логику формирования результатов: каждый сэмплер внутри контроллера будет считаться отдельным тестом со своим именем и описанием, которые будут браться из названия и описания сэмплера. Остальные поля будут общие для всех тестов в контроллере (серьёзность, эпик и т.д.).
  
- **Without content**: Если установлен, то для шагов теста в отчёте больше не будут формироваться приложенные файлы с деталями запроса и ответа. ВНИМАНИЕ! Если установлен чек-бокс "Stop test on error", то для шага с ошибкой приложенные файлы всё равно сформируются, чтобы можно было посмотреть причину ошибки.
  
- **Without non-HTTP steps**: Если установлен, то в шаги тест-кейса перестанут попадать все НЕ HTTP сэмплеры (например, JSR223 Sampler или Debug Sampler). ВНИМАНИЕ! Если установлен чек-бокс "Stop test on error", то даже если первым сэмплером с ошибкой (на котором поток остановится) станет НЕ HTTP сэмплер, то для него всё равно будет создан шаг, чтобы показать в отчёте причину остановки теста.
  
- **Debug mode**: Если установлен, то в отчёте ничего не поменяется, но файл .json с результатами теста приобретёт читабельный вид. Функция нужна только для ручной проверки результатов.
  
- **Test name + Description**: Название тест-кейса и его описание. Если установлен чек-бокс "Single step tests", то эти поля становятся недоступными для заполнения, потому что тогда у каждого сэмплера внутри контроллера будут свои название и описание.
  
- **Severity**: Указание серьёзности тест-кейса. По умолчанию в Allure доступны варианты: "trivial", "minor", "normal", "critical" и "blocker". Доступен свободный ввод. Если оставить поле пустым, то будет указано "normal".
  
- **Epic + Story + Feature**: Наименования соответствующих сущностей. Служат для разделения тест-кейсов в отчёте по функционалу.
  
- **Tags (comma-delimited)**: Указание тегов к тест-кейсу.
- **Params (comma-delimited)**: Указание переменных, которые нужно отразить в тест-кейсе. Заполняется именами переменных, без "${}". ВНИМАНИЕ! Если установлен чек-бокс "Single step tests", то для переменных будут браться те значения, которые они имеют после выполнения текущего сэмплера. Если чек-бокс "Single step tests" не установлен, то для переменных будут браться значения, которые они имеют до начала работы контроллера.

- **Owner**: Указание создателя тест-кейса либо ответственного за его актуальность.
  
- **Links (format: name-comma-URL)**: Указание ссылок к тест-кейсу. Каждая ссылка должна начинаться с новой строки. Если значение в строке не будет соответствовать формату из названия поля, то строка будет пропущена и не попадёт в отчёт.
  
- **Extra labels (Allure TMS only)**: Указание дополнительных атрибутов тест-кейса. Формат заполнения такой же, как и у "Links". ВНИМАНИЕ! При генерации отчёта при помощи консольной утилиты Allure, поддержка дополнительных полей не осуществляется. Указанные здесь значения должны поддерживаться вашей Allure TMS, чтобы быть видимыми в отчёте.

  
## Утверждения
Для каждого шага плагин анализирует все вложенные утверждения (assertions) и выводит их в отчёте. В случае, если хотя бы один из сэмплеров в тест-кейсе завершается с ошибкой, в отчёте в верхней части карточки тест-кейса будет отображаться failure message из первого утверждения, которое не выполнилось. Если в первом сэмплере с ошибкой не будет ни одного утверждения, которое не выполнилось, то failure message останется пустым, а причину ошибки можно будет найти в приложенных к шагу тест-кейса файлах с деталями запроса/ответа.


## Ограничения
1. В настоящий момент плагин не поддерживает переменные внутри шага тест-кейса. Доступны только переменные кейса целиком. Поддержка параметров шага тест-кейса появится в следующих версиях.
2. Если вы используете внутри Allure Test Controller какой-либо контроллер цикла (например, While Controller или ForEach Controller), то каждая итерация для сэмплеров внутри цикла будет создавать новые шаги тест-кейса в отчёте. Исключение: если вы используете [Retry Post-Processor](https://github.com/tilln/jmeter-retrier), то шаг будет создан только для последней попытки выполнения сэмплера.
3. Старайтесь не использовать Allure Test Controller внутри такого же контроллера. Если в дочернем контроллере будет установлен чек-бокс "Stop test on error", а в родительском - нет, то в случае ошибки внутри дочернего контроллера в отчёт не попадут результаты родительского, потому что поток будет остановлен до того, как результаты родительского контроллера будут записаны в файл.



# Механика селективного запуска

Помимо сохранения отчёта для Allure, плагин также сохраняет результаты выполнения каждого отдельного теста в папку "last-try-results", которая автоматически создаётся там же, где и папка "allure-results". Можно настроить сценарий таким образом, чтобы при запуске он пропускал все тесты, которые в прошлый раз выполнились успешно. 
Настройка осуществляется за счёт свойства JMeter "allure.retry.fallen". Если "allure.retry.fallen" = "true" - сработает механика селективного запуска и повторно будут выполняться только тесты, завершившиеся в последний раз с ошибками.
Данное свойство можно установить несколькими способами:
- **Прописать в файле .properties**: Делать это рекомендуется только в том случае, если вы хотите всегда по умолчанию включать данную опцию ("true").
  
- **Указать в сценарии при помощи сэмплера, поддерживающего ввод кода**: Например, для BeanShell Sampler установка свойства будет выглядеть следующим образом:
```Java
import org.apache.jmeter.util.JMeterUtils;
JMeterUtils.setProperty("allure.retry.fallen", "true");
```

- **Указать при запуске через терминал при помощи параметра -J**:
```
jmeter -Jallure.retry.fallen=true -n -t [your_jmx_file]
```


## Как механика селективного запуска влияет на сценарий и отчёт Allure:
1. Если "Single step tests" не установлен, то в случае, если в последний раз хотя бы один шаг теста завершился с ошибкой, в следующий раз весь тест будет выполнен и попадёт в отчёт. Если в последний раз тест завершился успешно, то в этот раз сэмплеры в нём не будут выполняться, а данный тест не попадёт в отчёт.
2. Когда "Single step tests" установлен: если хотя бы один из сэмплеров внутри контроллера в последний раз завершился с ошибкой, то выполняться в сценарии будут все сэмплеры внутри него. В отчёт при этом попадут только тесты, которые в предыдущий раз завершились с ошибкой, и тесты, которые завершились с ошибкой в этот раз. Например, внутри контроллера 5 сэмплеров. Если в предыдущий раз с ошибкой завершился только второй, а после текущего запуска ошибку выдали четвёртый и пятый, то в отчёт попадут 3 теста: 2, 4 и 5. Если в прошлый раз все сэмплеры внутри контроллера завершились успешно - выполнение будет пропущено и в отчёт эти тесты не попадут. ВНИМАНИЕ! Для записи результатов предыдущего запуска теста из одного шага используется уникальное сочетание "Thread Name" + "Sampler Name". Не используйте одинаковые имена сэмплеров внутри контроллера, если установлен "Single step tests", иначе результаты одних тестов будут стирать результаты других.
