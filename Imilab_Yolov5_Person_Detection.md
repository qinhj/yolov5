# Person Detection: Yolov5 #
[SINGLE-CLASS TRAINING EXAMPLE](https://github.com/ultralytics/yolov3/issues/102)  
```
==============================================================================
    model    | device | GPU Memory |    VmPeak   | Threads | Cpus_allowed_list
==============================================================================
   yolov5s   |  CPU   |  --------  | 15182444 kB |   34    |        0-7
------------------------------------------------------------------------------
   yolov5s   | CUDA:0 |  5581MiB   | 24389576 kB |   19    |        0-3
------------------------------------------------------------------------------
yolov5s-tiny | CUDA:0 |  4.05GB    | 24389576 kB |   19    |        0-3     X <-- not enough, need to rebuild network
==============================================================================
```

## Test Benchmark ##
```
## quick commands
$ export MODELS="yolov5l yolov5l6 yolov5m yolov5m6 yolov5s yolov5s6 yolov5x yolov5x6"
$ export OPTIONS="--data data/coco5k.yaml --img-size 640 --verbose --save-txt --save-conf"
$ for m in $MODELS; do python test.py --weights weights/${m}.pt --project runs/test/${m} $OPTIONS > runs/test/${m}.txt; done

## runtime Namespace(others)
batch_size=32, conf_thres=0.001, iou_thres=0.6, task='val', single_cls=False, augment=False,
verbose=True, save_txt=True, save_hybrid=False, save_conf=True, save_json=False, exist_ok=False

## output results
=====================================================================================================================================================
  model  | GPU Memory | Layers |  Parameters  | Gradients |  FLOPS  | Precision  Recall  mAP@0.5  mAP@0.5:0.95 | Rank
=====================================================================================================================================================
yolov5x  |   ------   |  476   |  87,730,285  |    0      | 219.0 G |     0.841   0.735    0.818      0.583    |    1
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5x6 |   ------   |  606   | 141,755,500  |    0      |  -----  |     0.831   0.727    0.807      0.574    |    2
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5l  |   ------   |  392   |  47,025,981  |    0      | 115.6 G |     0.837   0.724    0.808      0.566    |    3
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5l6 |   ------   |  501   |  77,218,620  |    0      |  -----  |     0.841   0.708    0.799      0.562    |    4
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5m  |   ------   |  308   |  21,356,877  |    0      |  51.4 G |     0.819   0.707    0.789      0.539    |    5   <-- out of memory
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5m6 |   ------   |  396   |  35,889,612  |    0      |  -----  |     0.819   0.706    0.784      0.539    |    6   <-- out of memory
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5s  |   5581MiB  |  224   |   7,266,973  |    0      |  17.1 G |     0.794   0.667    0.746      0.472    |    8   <-- pick to vs yolov3-tiny
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5s6 |   ------   |  291   |  12,653,596  |    0      |  -----  |     0.813   0.66     0.744      0.485    |    7   <-- miss yolov5s6.yaml
=====================================================================================================================================================
```

## Test Case 0: scratch without any pretrained weights ##
```
=====================================================================================================================================================
    model    | --device |        --data         |           --cfg          |         --hyp         | Precision Recall mAP@0.5 mAP@0.5:0.95
=====================================================================================================================================================
yolov5s      |  CUDA:0  | data/coco_person.yaml | models/yolov5s.yaml      | data/hyp.scratch.yaml |   0.8168  0.6948  0.7904  0.5260   <--  ^C (↑↑ 299/299)
-----------------------------------------------------------------------------------------------------------------------------------------------------
yolov5s-tiny |  CUDA:0  | data/coco_person.yaml | models/yolov5s-tiny.yaml | data/hyp.scratch.yaml |   0.6903  0.6422  0.6962  0.4195   <-- Fin (↓  288/299)
=====================================================================================================================================================

## train model
$ MODEL=yolov5s # yolov5m
$ OPTIONS="--data data/coco_person.yaml --single-cls --epochs 300 --batch-size 24"
$ python train.py --weights '' --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS
```

## Test Case 1: scratch/finetune with official weights (Y: seems OK) ##
```
$ MODEL=yolov5s
=====================================================================================================================================================
  yolov5s  | --device |      --weights      |        --cfg         |         --hyp          | Precision Recall mAP@0.5 mAP@0.5:0.95
=====================================================================================================================================================
(-) case1  |  CUDA:0  | weights/yolov5s.pt  | models/yolov5s.yaml  | data/hyp.scratch.yaml  |   0.8104  0.6782  0.7763  0.5080  <-- ^C  (↑  124/299)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) case2  |  CUDA:0  | weights/yolov5s.pt  | models/yolov5s.yaml  | data/hyp.finetune.yaml |   0.7931  0.7119  0.7907  0.5270  <-- Fin (↑↑ 232/299) (*)
=====================================================================================================================================================

$ MODEL=yolov5s6
=====================================================================================================================================================
  yolov5s6 | --device |     --weights       |        --cfg         |         --hyp          | Precision Recall mAP@0.5 mAP@0.5:0.95
=====================================================================================================================================================
(-) case1  |  CUDA:0  | weights/yolov5s6.pt | models/yolov5s6.yaml | data/hyp.scratch.yaml  |   (out of memory)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) case2  |  CUDA:0  | weights/yolov5s6.pt | models/yolov5s6.yaml | data/hyp.finetune.yaml |   (out of memory)
=====================================================================================================================================================

## train model
$ OPTIONS="--data data/coco_person.yaml --single-cls --epochs 300 --batch-size 24"
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.scratch.yaml
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.finetune.yaml

* Common Options
data: data/coco_person.yaml
epochs: 300
batch_size: 24
img_size: [640, 640]
multi_scale: false
single_cls: true
adam: false
```

## Test Case 2: scratch/finetune with warmup weights on val data (X: seems overfit a lot) ##
Plz review https://github.com/ultralytics/yolov3/issues/1648#issuecomment-756552051
```
$ MODEL=yolov5s
=====================================================================================================================================================
  yolov5s   | --epochs | --device |       --weights        |         --hyp          |         --data          | Precision Recall mAP@0.5 mAP@0.5:0.95
=====================================================================================================================================================
(↑) step1.0 |   10/20  |   CPU    | weights/yolov5s.pt     | data/hyp.scratch.yaml  | data/coco_person5k.yaml |   0.8639  0.7508  0.8478  0.5603  <-- Fin (↑↑ 19/19)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) step1.1 |   200    |  CUDA:0  | warmup/weights/best.pt | data/hyp.scratch.yaml  | data/coco_person.yaml   |   0.8078  0.6732  0.7703  0.4972  <--  ^C (↑  51/199)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(↑) step1.2 |   200    |  CUDA:0  | warmup/weights/best.pt | data/hyp.finetune.yaml | data/coco_person.yaml   |   0.8088  0.7044  0.7929  0.5249  <--  ^C (↘ 67/199)
=====================================================================================================================================================
(↑) step2.0 |    20    |   CPU    | weights/yolov5s.pt     | data/hyp.finetune.yaml | data/coco_person5k.yaml |   0.7888  0.684   0.7588  0.4764  <-- Fin (-  19/19)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) step2.1 |   200    |  CUDA:0  | warmup/weights/best.pt | data/hyp.scratch.yaml  | data/coco_person.yaml   |   0.8407  0.6788  0.7916  0.5277  <-- Fin (↑↑ 199/199) (*)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) step2.2 |   200    |  CUDA:0  | warmup/weights/best.pt | data/hyp.finetune.yaml | data/coco_person.yaml   |   0.8167  0.693   0.7896  0.5252  <-- Fin (↑↑ 180/199) (*)
=====================================================================================================================================================

$ MODEL=yolov5s-tiny
=====================================================================================================================================================
yolov5s-tiny | --epochs | --device |    --weights    |          --hyp         |         --data          | Precision Recall mAP@0.5 mAP@0.5:0.95
=====================================================================================================================================================
(↑) step1.0  |    40    |   CPU    |       ''        | data/hyp.scratch.yaml  | data/coco_person5k.yaml |   0.5778  0.4328  0.4718  0.2128  <-- Fin (↓↓ 39/39)  (* It seems scratch with a small datasets is useless.)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) step1.1  |   300    |  CUDA:0  | warmup.scratch/ | data/hyp.scratch.yaml  | data/coco_person.yaml   |   0.665   0.6107  0.6603  0.3834  <--  ^C (↓↓ 86/299)
-----------------------------------------------------------------------------------------------------------------------------------------------------
(-) case1.2  |   300    |  CUDA:0  | warmup.scratch/ | data/hyp.finetune.yaml | data/coco_person.yaml   |   0.6652  0.6004  0.65    0.3746  <--  ^C (↓↓ 165/299)
=====================================================================================================================================================

## train model: warmup
$ OPTIONS="--data data/coco_person5k.yaml --single-cls --epochs 20 --batch-size 24"
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.scratch.yaml
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.finetune.yaml
## train model: scratch/finetune
$ OPTIONS="--data data/coco_person.yaml --single-cls --epochs 200 --batch-size 24"
$ python train.py --weights warmup/weights/best.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.scratch.yaml
$ python train.py --weights warmup/weights/best.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.finetune.yaml

* Common Options
batch_size: 24
img_size: [640, 640]
multi_scale: false
single_cls: true
adam: false
```

## Test Results ##
```
0. yolov5s-tiny isn't as good as expected;
1. pretrained + finetune/scratch is OK and maybe enouth;
2. pretrained + warmup(finetune on small data) + finetune/scratch is acceptable;
3. pretrained + warmup(scratch on small data) + finetune/scratch isn't suggested;
```