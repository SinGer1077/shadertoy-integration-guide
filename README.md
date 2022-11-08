# shadertoy-integration-guide
Гайд для использования шейдеров с shadertoy в babylon.js и glslCanvas'е

## Последовательность действий:

1. Заходим на сайт ShaderToy: https://www.shadertoy.com/.
2. Ищем и выбираем понравившийся, кликаем на него (например: https://www.shadertoy.com/view/DdSGzy). Появляется код шейдера наподобие этого:![image](https://user-images.githubusercontent.com/35637110/200515890-7d156489-1fb0-4f91-b37f-f1c4f64f898b.png)
3. В ShaderToy'е используется свой интерфейс, который не получится просто скопировать и использовать где угодно. Но сделать это довольно легко.

## Вариант 1: Babylon.js
### Если используем CYOS
1. Заходим на https://cyos.babylonjs.com/. Тут можно экспериментировать, тем более vertex шейдер уже готов.
2. Выбираем нужный нам мэш и Basic template.
3. Вершинный шейдер трогать не надо, он уже готов. Смотрим на фрагментный:

![image](https://user-images.githubusercontent.com/35637110/200520382-bcd0e8fc-90a4-42b1-b33f-eed623879ba8.png)

Первая строка задаёт точность вычислений, её оставляем. 

Вторая переменная vUV связана с вершинным шейдером, её оставляем. 

textureSample нам не нужен, тут он задаёт дефолтную текстуру - эту переменную можно удалить. 

void main(void) - вход программы, её обязательно оставляем. 

4. Копируем весь код из shadertoy'я и вставляем между переменными и функцией main.
5. Теперь нужно проследить за некоторыми вещами. Во-первых: 
  - В CYOS точкей входа служит main, а в shadertoy'е - mainImage. Нужно, чтобы mainImage вызывался в main (и проследить, чтобы корректно были указаны параметры):
   
![image](https://user-images.githubusercontent.com/35637110/200522384-f58cd782-91d4-457f-acab-7b492c7fa9f4.png)

  - В Shadertoy и в CYOS различается синтаксис, а именно - названия встроенных переменных. Два самых очевидных примера: iTime и iResolution. 
  - iTime имеет аналог в CYOS - просто "time". Для использования нужно объявить переменную uniform float time. А также не забыть переименовать все переменные в коде с iTime на time, либо объявить #define iTime time:
   
![image](https://user-images.githubusercontent.com/35637110/200525611-047958cf-1243-44b9-bda7-b69808b1279a.png)

  - На момент написания гайда в CYOS не был найден аналог iResolution. Однако, экспериментально было выяснено, что изображение будет адекватным при значениях vec2(1000,1000):

![image](https://user-images.githubusercontent.com/35637110/200525939-e402e00d-6342-49d7-b080-c9eca64379c1.png)

Полученный результат:

![image](https://user-images.githubusercontent.com/35637110/200526031-697a4c0e-b34d-482d-9218-257820cecb32.png)

Если были получены ошибки, посмотрите на их содержание: скорее всего, нужно поменять название какой-то переменной или внимательнее проверить, какие переменные указываются в качестве параметров в функции mainImage.

### Если используем чистый Canvas.
Этот предмент собственноручно не тестировался. Однако, был найден показательный sandbox: https://codesandbox.io/s/lbq4e?file=/src/index.js.

При просмотре кода видно, что используется некое хранилище BABYLON.Effect.ShadersStore, в которое можно занести отдельно как вершинный шейдер, так и фрагментный. Вершинный уже установлен по умолчанию, модифицируем только фрагментный:
1. Процесс точно такой же, как и при работе с CYOS. Копируем и вставляем код. При этом, в main вызываем mainImage, внимательно смотрим какие параметры передаются
2. Опять же, внимательно смотрим названия переменных. Здесь уже используются такие же iTime и iResolution, как в Shadertoy'е.
3. Результат:

![image](https://user-images.githubusercontent.com/35637110/200529303-66ebf89f-e0ea-41c4-8977-df5166cfca34.png)

## Вариант 2: glslCanvas
### Использование в чистом canvas'е
1. Заходим на репозиторий https://github.com/patriciogonzalezvivo/glslCanvas
2. В нужном нам html файле добавляем строку <canvas class="glslCanvas" data-fragment-url="shader.frag" width="500" height="500"></canvas>. Фрагментный шейдер можно использовать или через аттрибут "data-fragment-url", указав путь к файлу .frag, или черз аттрибут "data-fragment", указав в нём весь код шейдера.
В первом случае могут быть проблемы из-за CORS policy.
4. Для использования glslCanvas нужно использоват GlslCanvas.js скрипт. Достучаться до него можно двумся способами:
  - Указать ссылкой: <script type="text/javascript" src="https://rawgit.com/patriciogonzalezvivo/glslCanvas/master/dist/GlslCanvas.js"></script>
  - Сделать npm install glslCanvas в нужную папку, затем указать путь до скрипта: <script type="text/javascript" src="node_modules/glslCanvas/lib/GlslCanvas.js"></script>
5. Результат:

![image](https://user-images.githubusercontent.com/35637110/200546176-e2ea3889-fac5-415b-8565-376bd883e06a.png)

6. 
