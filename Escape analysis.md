Escape analysis — это процесс, который компилятор использует для определения размещения значений, созданных вашей программой.

В частности, компилятор выполняет статический анализ кода, чтобы определить, может ли значение быть помещено в стековый фрейм для функции, которая его строит, или значение должно "сбежать" в кучу. Используется разработчиками для оптимизации кода и аналитики причин возможного замедления.

Команда для запуска escape-анализа: `go build -gcflags="-m"` (так же можно использовать флаги `-N` для отключения оптимизаций, `-l` для отключения "инлайнинга").
