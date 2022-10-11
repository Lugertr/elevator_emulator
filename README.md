# Эмулятор лифта

## Для разработки использовался vue.cli для Vue 3. 
## Масштабирование происходит путем изменения значений в файле 

##src/component/data/appConfig.js

```
Анимации лифта реализованы с помощью CSS:
  keyframes для мигания
  transition для движения
```

```
Для сохранения состояния при обновлении страницы использовался localStorage

Сохраняется только устойчивое состояние - состояние покоя и состояние начала обработки очередного запроса.
(т.е. если у лифта происходит анимация мигания или движения, а страница обновилась, то лифт начнёт анимацию заново)
 ```
## Состояние "отдыха" у лифта
В ТЗ указано, что лифт нельзя вызвать если на этом этаже находится лифт в состоянии покоя.
А так, как мигающий лифт не находится в состоянии покоя, то кнопку вызова нажать можно.
 
