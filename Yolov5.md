## Quick Env ##
```
see Yolov3.md, section "Quick Env".
```

## Quick Note ##
```
* YOLOv5-P5 models (same architecture as v4.0 release): 3 output layers P3, P4, P5 at strides 8, 16, 32, trained at --img 640
* YOLOv5-P6 models: 4 output layers P3, P4, P5, P6 at strides 8, 16, 32, 64 trained at --img 1280
```

## Quick Start ##
```
## download pretrained weights(v5)
$ export MODELS="yolov5l yolov5l6 yolov5m yolov5m6 yolov5s yolov5s6 yolov5x yolov5x6"
$ for m in $MODELS; do wget https://github.com/ultralytics/yolov5/releases/download/v5.0/${m}.pt
## download pretrained weights(v4)
$ wget https://github.com/ultralytics/yolov5/releases/download/v4.0/yolov5s.onnx

## detect
$ export MODELS="yolov5l yolov5l6 yolov5m yolov5m6 yolov5s yolov5s6 yolov5x yolov5x6"
$ export OPTIONS="--source data/images --img-size 640 --save-txt --save-conf"
$ for m in $MODELS; do python detect.py --weights weights/${m}.pt --project runs/detect/${m} $OPTIONS

## test
$ export MODELS="yolov5l yolov5l6 yolov5m yolov5m6 yolov5s yolov5s6 yolov5x yolov5x6"
$ export OPTIONS="--data data/coco128.yaml --img-size 640 --verbose --save-txt --save-conf" # --single-cls
$ for m in $MODELS; do python test.py --weights weights/${m}.pt --project runs/test/${m} $OPTIONS > runs/test/${m}.txt; done

## train
$ wget https://github.com/ultralytics/yolov5/releases/download/v1.0/coco128.zip
$ export MODELS="yolov5l yolov5m yolov5s yolov5x"
$ export OPTIONS="--data data/coco128.yaml --epochs 20 --batch-size 16 --img-size 640" # --rect --device --single-cls --adam
$ for m in $MODELS; do python train.py --weights weights/${m}.pt --cfg models/${m}.yaml --project runs/train/${m} $OPTIONS; done

* Note for Window:
> SET OPTIONS=--data data/coco128.yaml --img-size 640 --verbose --save-txt --save-conf
> echo %OPTIONS%
```

## Quick Models (Train) ##
```
* yolov5l
================================================================================
                 from  n    params  module                                  arguments                     
  0                -1  1      7040  models.common.Focus                     [3, 64, 3]                    
  1                -1  1     73984  models.common.Conv                      [64, 128, 3, 2]               
  2                -1  1    156928  models.common.C3                        [128, 128, 3]                 
  3                -1  1    295424  models.common.Conv                      [128, 256, 3, 2]              
  4                -1  1   1611264  models.common.C3                        [256, 256, 9]                 
  5                -1  1   1180672  models.common.Conv                      [256, 512, 3, 2]              
  6                -1  1   6433792  models.common.C3                        [512, 512, 9]                 
  7                -1  1   4720640  models.common.Conv                      [512, 1024, 3, 2]             
  8                -1  1   2624512  models.common.SPP                       [1024, 1024, [5, 9, 13]]      
  9                -1  1   9971712  models.common.C3                        [1024, 1024, 3, False]        
 10                -1  1    525312  models.common.Conv                      [1024, 512, 1, 1]             
 11                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 12           [-1, 6]  1         0  models.common.Concat                    [1]                           
 13                -1  1   2757632  models.common.C3                        [1024, 512, 3, False]         
 14                -1  1    131584  models.common.Conv                      [512, 256, 1, 1]              
 15                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 16           [-1, 4]  1         0  models.common.Concat                    [1]                           
 17                -1  1    690688  models.common.C3                        [512, 256, 3, False]          
 18                -1  1    590336  models.common.Conv                      [256, 256, 3, 2]              
 19          [-1, 14]  1         0  models.common.Concat                    [1]                           
 20                -1  1   2495488  models.common.C3                        [512, 512, 3, False]          
 21                -1  1   2360320  models.common.Conv                      [512, 512, 3, 2]              
 22          [-1, 10]  1         0  models.common.Concat                    [1]                           
 23                -1  1   9971712  models.common.C3                        [1024, 1024, 3, False]        
--------------------------------------------------------------------------------
 24      [17, 20, 23]  1    457725  models.yolo.Detect                      [80, [[10, 13, 16, 30, 33, 23], [30, 61, 62, 45, 59, 119], [116, 90, 156, 198, 373, 326]], [256, 512, 1024]]
Model Summary: 499 layers, 47056765 parameters, 47056765 gradients, 115.6 GFLOPS
================================================================================
Transferred 648/650 items from weights/yolov5l.pt
Scaled weight_decay = 0.0005
Optimizer groups: 110 .bias, 110 conv.weight, 107 other

* yolov5m
================================================================================
                 from  n    params  module                                  arguments                     
  0                -1  1      5280  models.common.Focus                     [3, 48, 3]                    
  1                -1  1     41664  models.common.Conv                      [48, 96, 3, 2]                
  2                -1  1     65280  models.common.C3                        [96, 96, 2]                   
  3                -1  1    166272  models.common.Conv                      [96, 192, 3, 2]               
  4                -1  1    629760  models.common.C3                        [192, 192, 6]                 
  5                -1  1    664320  models.common.Conv                      [192, 384, 3, 2]              
  6                -1  1   2512896  models.common.C3                        [384, 384, 6]                 
  7                -1  1   2655744  models.common.Conv                      [384, 768, 3, 2]              
  8                -1  1   1476864  models.common.SPP                       [768, 768, [5, 9, 13]]        
  9                -1  1   4134912  models.common.C3                        [768, 768, 2, False]          
 10                -1  1    295680  models.common.Conv                      [768, 384, 1, 1]              
 11                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 12           [-1, 6]  1         0  models.common.Concat                    [1]                           
 13                -1  1   1182720  models.common.C3                        [768, 384, 2, False]          
 14                -1  1     74112  models.common.Conv                      [384, 192, 1, 1]              
 15                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 16           [-1, 4]  1         0  models.common.Concat                    [1]                           
 17                -1  1    296448  models.common.C3                        [384, 192, 2, False]          
 18                -1  1    332160  models.common.Conv                      [192, 192, 3, 2]              
 19          [-1, 14]  1         0  models.common.Concat                    [1]                           
 20                -1  1   1035264  models.common.C3                        [384, 384, 2, False]          
 21                -1  1   1327872  models.common.Conv                      [384, 384, 3, 2]              
 22          [-1, 10]  1         0  models.common.Concat                    [1]                           
 23                -1  1   4134912  models.common.C3                        [768, 768, 2, False]          
--------------------------------------------------------------------------------
 24      [17, 20, 23]  1    343485  models.yolo.Detect                      [80, [[10, 13, 16, 30, 33, 23], [30, 61, 62, 45, 59, 119], [116, 90, 156, 198, 373, 326]], [192, 384, 768]]
Model Summary: 391 layers, 21375645 parameters, 21375645 gradients, 51.4 GFLOPS
================================================================================
Transferred 504/506 items from weights/yolov5m.pt
Scaled weight_decay = 0.0005
Optimizer groups: 86 .bias, 86 conv.weight, 83 other

* yolov5s
================================================================================
                 from  n    params  module                                  arguments                     
  0                -1  1      3520  models.common.Focus                     [3, 32, 3]                    
  1                -1  1     18560  models.common.Conv                      [32, 64, 3, 2]                
  2                -1  1     18816  models.common.C3                        [64, 64, 1]                   
  3                -1  1     73984  models.common.Conv                      [64, 128, 3, 2]               
  4                -1  1    156928  models.common.C3                        [128, 128, 3]                 
  5                -1  1    295424  models.common.Conv                      [128, 256, 3, 2]              
  6                -1  1    625152  models.common.C3                        [256, 256, 3]                 
  7                -1  1   1180672  models.common.Conv                      [256, 512, 3, 2]              
  8                -1  1    656896  models.common.SPP                       [512, 512, [5, 9, 13]]        
  9                -1  1   1182720  models.common.C3                        [512, 512, 1, False]          
 10                -1  1    131584  models.common.Conv                      [512, 256, 1, 1]              
 11                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 12           [-1, 6]  1         0  models.common.Concat                    [1]                           
 13                -1  1    361984  models.common.C3                        [512, 256, 1, False]          
 14                -1  1     33024  models.common.Conv                      [256, 128, 1, 1]              
 15                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 16           [-1, 4]  1         0  models.common.Concat                    [1]                           
 17                -1  1     90880  models.common.C3                        [256, 128, 1, False]          
 18                -1  1    147712  models.common.Conv                      [128, 128, 3, 2]              
 19          [-1, 14]  1         0  models.common.Concat                    [1]                           
 20                -1  1    296448  models.common.C3                        [256, 256, 1, False]          
 21                -1  1    590336  models.common.Conv                      [256, 256, 3, 2]              
 22          [-1, 10]  1         0  models.common.Concat                    [1]                           
 23                -1  1   1182720  models.common.C3                        [512, 512, 1, False]          
--------------------------------------------------------------------------------
 24      [17, 20, 23]  1    229245  models.yolo.Detect                      [80, [[10, 13, 16, 30, 33, 23], [30, 61, 62, 45, 59, 119], [116, 90, 156, 198, 373, 326]], [128, 256, 512]]
Model Summary: 283 layers, 7276605 parameters, 7276605 gradients, 17.1 GFLOPS
================================================================================
Transferred 360/362 items from weights/yolov5s.pt
Scaled weight_decay = 0.0005
Optimizer groups: 62 .bias, 62 conv.weight, 59 other

* yolov5x
================================================================================
                 from  n    params  module                                  arguments                     
  0                -1  1      8800  models.common.Focus                     [3, 80, 3]                    
  1                -1  1    115520  models.common.Conv                      [80, 160, 3, 2]               
  2                -1  1    309120  models.common.C3                        [160, 160, 4]                 
  3                -1  1    461440  models.common.Conv                      [160, 320, 3, 2]              
  4                -1  1   3285760  models.common.C3                        [320, 320, 12]                
  5                -1  1   1844480  models.common.Conv                      [320, 640, 3, 2]              
  6                -1  1  13125120  models.common.C3                        [640, 640, 12]                
  7                -1  1   7375360  models.common.Conv                      [640, 1280, 3, 2]             
  8                -1  1   4099840  models.common.SPP                       [1280, 1280, [5, 9, 13]]      
  9                -1  1  19676160  models.common.C3                        [1280, 1280, 4, False]        
 10                -1  1    820480  models.common.Conv                      [1280, 640, 1, 1]             
 11                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 12           [-1, 6]  1         0  models.common.Concat                    [1]                           
 13                -1  1   5332480  models.common.C3                        [1280, 640, 4, False]         
 14                -1  1    205440  models.common.Conv                      [640, 320, 1, 1]              
 15                -1  1         0  torch.nn.modules.upsampling.Upsample    [None, 2, 'nearest']          
 16           [-1, 4]  1         0  models.common.Concat                    [1]                           
 17                -1  1   1335040  models.common.C3                        [640, 320, 4, False]          
 18                -1  1    922240  models.common.Conv                      [320, 320, 3, 2]              
 19          [-1, 14]  1         0  models.common.Concat                    [1]                           
 20                -1  1   4922880  models.common.C3                        [640, 640, 4, False]          
 21                -1  1   3687680  models.common.Conv                      [640, 640, 3, 2]              
 22          [-1, 10]  1         0  models.common.Concat                    [1]                           
 23                -1  1  19676160  models.common.C3                        [1280, 1280, 4, False]        
--------------------------------------------------------------------------------
 24      [17, 20, 23]  1    571965  models.yolo.Detect                      [80, [[10, 13, 16, 30, 33, 23], [30, 61, 62, 45, 59, 119], [116, 90, 156, 198, 373, 326]], [320, 640, 1280]]
Model Summary: 607 layers, 87775965 parameters, 87775965 gradients, 219.0 GFLOPS
================================================================================
Transferred 792/794 items from weights/yolov5x.pt
Scaled weight_decay = 0.0005
Optimizer groups: 134 .bias, 134 conv.weight, 131 other
```

## Quick FAQ ##
```
See Yolov3.md, section "Quick FAQ".
```
