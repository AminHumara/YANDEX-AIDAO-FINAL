# YANDEX-AIDAO-FINAL

## Описание задачи

Проект направлен на использование алгоритмов машинного и глубокого обучения для автоматической проверки фотографий автомобилей, предоставляемых водителями сервиса Yandex GO. Целью является оценка качества как самих фотографий, так и состояния автомобиля. Это позволяет выявлять мошеннические действия (например, размытые или неправильно сделанные снимки) и повреждения транспортных средств.

### Подробности задачи:
1. **Фотографии автомобилей**: Водители должны сделать 4 фотографии своего автомобиля (спереди, сзади, слева, справа).
2. **Автоматическая проверка**: Используя обученные модели машинного обучения, система должна определить, требуется ли дополнительная проверка человеком или можно автоматически утвердить инспекцию.
3. **Метрика качества**: Используется ROC AUC для оценки эффективности модели на уровне всей инспекции, а не отдельных фотографий.

### Примеры проблем:
- Фотография слишком темная (`DARK_PHOTO`).
- На изображении видна только часть автомобиля (`INCOMPLETE_CAPTURE`).
- Фото сделано со экрана другого устройства (`SCREEN_PHOTO`).

## Решение

Решение включает несколько этапов:

### 1. Подготовка данных
- Данные представлены в виде набора фотографий автомобилей и соответствующих меток (метки фрода и повреждений).
- Каждая инспекция содержит 4 фотографии, и каждая фотография имеет метку, указывающую на возможные проблемы (`fraud_verdict`) и состояние автомобиля (`damage_verdict`).

### 2. Предобработка данных
- Фотографии преобразуются в тензоры с использованием стандартной нормализации ImageNet.
- Размер изображений изменяется до `(224, 224)` пикселей.
- Для каждой фотографии создаются целевые метки, которые объединяются для формирования общего результата инспекции.

### 3. Модель
- Использована предобученная модель ResNet18.
- Архитектура модифицирована: добавлены новые полносвязные слои для классификации.
- Критерий потерь: взвешенный бинарный кросс-энтропийный loss, учитывающий дисбаланс классов.

### 4. Обучение модели
- Модель обучается на GPU с использованием оптимизатора AdamW.
- Learning rate регулируется с помощью `StepLR`.
- Процесс обучения контролируется с помощью функции `train_model`, которая сохраняет лучшие веса модели.

### 5. Тестирование и оценка
- После завершения обучения модель тестируется на тестовом наборе данных.
- Вычисляется метрика ROC AUC для оценки общей производительности модели.

### 6. Создание решения для сабмита
- Скрипт `run.py` загружает модель и создает файл с прогнозами (`predictions.csv`), содержащий вероятности мошенничества для каждой инспекции.
- Все необходимые файлы (включая модель, скрипты и данные) упаковываются в `.zip` для отправки.

## Файлы проекта

- **baseline_real_fulldata_resnet18_9epoch.pt**: Веса обученной модели.
- **run.py**: Скрипт для выполнения предсказаний на новых данных.
- **prepare.py**: Скрипт подготовки данных (в данном случае ничего не делает).
- **Makefile**: Описывает процесс сборки и выполнения.
- **utils.py**: Утилиты для обработки данных, создания dataloader'ов и визуализации результатов.

## Зависимости

Для запуска проекта требуются следующие библиотеки:
- `torch`
- `torchvision`
- `pandas`
- `numpy`
- `tqdm`
- `scikit-learn`

Установка зависимостей:
```bash
pip install torch torchvision pandas numpy tqdm scikit-learn
```

## Выводы
Модель успешно обучена для детектирования фрода и повреждений автомобилей на основе фотографий. Использование предобученных сетей и корректной предобработки данных позволило достичь высокой точности. Дальнейшее улучшение может быть достигнуто через более сложные архитектуры моделей и увеличение объема данных.
