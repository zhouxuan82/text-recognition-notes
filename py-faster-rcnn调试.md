## py-faster-rcnn调试

### error1

```
Traceback (most recent call last):
  File "./tools/train_net.py", line 113, in <module>
    max_iters=args.max_iters)
  File "/home/ys/pycaffe/py-faster-rcnn/tools/../lib/fast_rcnn/train.py", line 157, in train_net
    pretrained_model=pretrained_model)
  File "/home/ys/pycaffe/py-faster-rcnn/tools/../lib/fast_rcnn/train.py", line 43, in __init__
    self.solver = caffe.SGDSolver(solver_prototxt)
  File "/home/ys/pycaffe/py-faster-rcnn/tools/../lib/roi_data_layer/layer.py", line 87, in setup
    layer_params = yaml.load(self.param_str_)
AttributeError: 'RoIDataLayer' object has no attribute 'param_str_'
```

error2

```
Traceback (most recent call last):
  File "./tools/train_net.py", line 113, in <module>
    max_iters=args.max_iters)
  File "/home/ys/pycaffe/py-faster-rcnn/tools/../lib/fast_rcnn/train.py", line 160, in train_net
    model_paths = sw.train_model(max_iters)
  File "/home/ys/pycaffe/py-faster-rcnn/tools/../lib/fast_rcnn/train.py", line 101, in train_model
    self.solver.step(1)
  File "/home/ys/pycaffe/py-faster-rcnn/tools/../lib/rpn/proposal_layer.py", line 65, in forward
    pre_nms_topN  = cfg[cfg_key].RPN_PRE_NMS_TOP_N
KeyError: '0'
```

error3

```
F0128 20:07:52.167606 25054 sgd_solver.cu:19] Check failed: error == cudaSuccess (11 vs. 0)  invalid argument
*** Check failure stack trace: ***
Aborted (core dumped)
```

