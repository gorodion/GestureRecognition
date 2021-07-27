# GestureRecognition
## Task
Дан огромный датасет с фотографиями, на которых люди показывают один из шести жестов, либо не показывают вовсе. Каждой фотографии присвоен класс. Необходимо обучить модель, определяющую, какой жест изображён на фотографии. Задача решалась в рамках соревнования MachinesCanSee на платформе [Boosters.Pro](https://boosters.pro/championship/machinescansee2021/overview)

## Pipeline solution
1. **Детектирование кистей**
  1. **Сбор и обработка данных.** Здесь мы собираем данные с размеченными кистями и их координатами. После сбора данных мы приводим их в единый формат.
  2. **Обучение детектора кистей.** В качестве модели была взята модель [YOLOv5x](https://github.com/ultralytics/yolov5)
2. **Классификация жестов**
  1. **Инференс детектора кистей.** Теперь когда у нас есть обученный детектор кистей мы можем прогнать через него фотографии из нашего датасета с жестами. В результате получим для каждого изображения координаты боксов, в которых есть кисти
  2. **Постобработка локализаций рук.** Мы оставляем верних бокс с кистью, так как в подавляющем числе случаев целевой жест показывается верхней рукой. Также мы расширяем наш бокс, чтобы кисть наверняка оказалась на обрезанной фотографии (кропе).
  3. **Обучение классификатора жестов.** На вход классификатору подаётся верхний бокс с кистью, классификатор обучается на метках жестов. В качестве модели была взята EfficientNetV2-M
3. **End-to-end Inference.** Здесь мы делаем функцию инференса обеих моделей. То есть на вход функции подаётся фотография, а на выходе выдаётся наиболее вероятный жест, который был детектирован на этой фотографии (или "no gesture"). Важный момент: мы берём все боксы с кистями на фотографии, после чего пропускаем их через классификатор и берём класс с максимальной вероятностью среди всех боксов.
Вы можете посмотреть весь код, перейдя на [google colab](https://colab.research.google.com/drive/1ADusHK40qO2Eha4O_xhcwPTV1aRQY5qP?usp=sharing)
<img src="https://colab.research.google.com/drive/1ADusHK40qO2Eha4O_xhcwPTV1aRQY5qP?usp=sharing" alt="Open In Colab" data-canonical-src="https://colab.research.google.com/assets/colab-badge.svg">

## Links
[Описание задачи с данными](https://boosters.pro/championship/machinescansee2021/overview)
[Данные & Модели](https://drive.google.com/drive/folders/18Qh5VPKK5c0XpJO742aK3mPwJbHK4aGB?usp=sharing)
[Модель детектора кистей](https://drive.google.com/file/d/1-CELzTRZObz9dGD28pB0xqeKrUKTtJc5/view?usp=sharing) (YOLOv5)
[Модель классификатора](https://drive.google.com/file/d/1aY4NxgXx8hY4NI8u-0Slj_d7z8qiRH2S/view?usp=sharing) (для импорта нужен модуль [timm](https://github.com/rwightman/pytorch-image-models.git))
[Ещё раз ссылка на код](https://colab.research.google.com/drive/1ADusHK40qO2Eha4O_xhcwPTV1aRQY5qP?usp=sharing)
[First hand dataset source](https://www.robots.ox.ac.uk/~vgg/data/hands/)
[Second hand dataset source](https://www3.cs.stonybrook.edu/~cvl/projects/hand_det_attention/)

## Unimplemented
* Дообучение детектора кистей на датасете жестов. То есть мы получаем координаты кистей от предобученного детектора для наших данных с жестами, после чего уточняем их вручную.
* Обучение классификатора на всем массиве данных с жестами. К сожалению, не хватило времени, хотя размер тренировочных данных сильно влиял на обученность модели (обучение было произведено на половине данных)
