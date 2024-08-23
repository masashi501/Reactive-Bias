# Reactive-Bias

## Requirements
```
pip3 install -r requirements.txt
```
The code was tested with Python 3.8.10

学習・評価用のデータセットは以下からダウンロードしてください
CUB-200-2011:https://github.com/cyizhuo/CUB-200-2011-dataset
CUB-GHA:https://github.com/yaorong0921/CUB-GHA

ダウンロードしたCUB-GHAは以下の様にCUBGHA_rename.pyを用いてファイル名の変更してください．
また，CUB_GHAはCUB_200_2011の学習・評価データセットと同じディレクトリに配置してください.
```
#CUB_200_2011
$ls
CUB_GHA   images.txt    val    images    train    split.py
$python CUBGHA_rename.py
```

## スクラッチ学習
imagenetでスクラッチ学習をする場合は以下のコマンドを使用してください
```
$ python3 -m torch.distributed.launch --nproc_per_node=4 --use_env main.py --model deit_small_patch16_224_12 --mixup 0.8 --cutmix 1.0 --batch-size 128 --epochs 300 --num_workers 40 --data-path $PATH --data-set CUB --output_dir $SAVE_PATH
```
--data-pathの$PATHにはデータセットのパスを入力してください．
--output_dirの$SAVE_PATHは学習後のcheckpointの保存先を入力してください．
デフォルトでは0 epochとBestと最新の3つのcheckpointを保存します．

## CUB-200-2011の学習
Imagenet等で学習したモデルをCUB-200-2011の学習に使用します．
```
$ python3 -m torch.distributed.launch --nproc_per_node=4 --use_env main.py --model deit_small_patch16_224_12 --mixup 0.8 --cutmix 1.0 --batch-size 128 --epochs 100 --num_workers 40 --data-path $PATH --data-set CUB --finetune $SAVED_PATH --output_dir $SAVE_PATH
```
--finetuneの$SAVED_PATHには前段のcheckpointのパスを入力してください．
--output_dirのパスは前段とは別の名称に変更してください．

## 人の知見の組み込み
CUB-200-2011で学習したモデルへ人の知見を組み込みます．
```
$ python3 -m torch.distributed.launch --nproc_per_node=4 --use_env main_hft.py --model deit_small_patch16_224_12_hum_00 --mixup 0.0 --cutmix 0.0 --batch-size 128 --epochs 100 --num_workers 40 --data-path $PATH --data-set CUBATT --human --hname $HUM_NAME --finetune $SAVED_PATH --output_dir $SAVE_PATH
```
--hnameのHUM_NAMEにはCUB-GHAのディレクトリ名を有力してください．
CUBGHA_rename.pyを使用した場合はGHAと入力してください．
