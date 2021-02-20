# Lightning-Flash
1. Pytorch Lightning Flash basics
Flashは、PyTorch Lightning上で構築されていたディープラーニングを高速に利用できるためのキットです。
Azure AutoMLに近い形でプロフェッショナルが作ったモデルを流用して、自らのディープラーニングモデルを作成することができます。
FlashがベースとなっているPyTorch LightningはとっつきにくいPyTorchをラップしたものですが、正直簡単にPyTorchが使えるようになった？という感じで、初心者がいきなり使いこなせるものではありません。
FlashはそのLightningをラップしており、Pythonを知っていて、回帰や分類などの機械学習の概念を知っている人であれば使えるレベルでまとまっています。

2. FlashがAIとして出来ること
Flashが現在サポートしているものとして、画像分類(Image Classification)、画像埋め込み(Image Embedding)、表形式データを使った分類(Tabular classification)、テキスト分類(Text classification)、要約(Summarization)、翻訳(Translation)など主だったものをサポート済みで、他の機能も現在開発中です。
Flashでは、これらの機能を実現する手段をタスクと定義し、予測したいクラスラベルの数、データセットの列数、損失関数、オプティマイザなど使用されるすべての関連情報が含まれています。
Flashは、[Open dataset](https://azure.microsoft.com/ja-jp/services/open-datasets/catalog/)や[Kaggle](https://www.kaggle.com/)のデータセットがあれば直ちに始めることができます。

3. やってみましょう！
Flashではすでに学習済みのモデルがタスクに用意されているため、いきなり推論を実施することができます。
推論は、訓練されたモデルから予測値を生成するプロセスです。すべてのFlashタスクには、事前に訓練された重みがあり、すぐに使用することができます。

# import
from flash import Trainer
from flash.core.data import download_data
from flash.vision import ImageClassificationData, ImageClassifier

# 1. Get dataset
download_data("https://pl-flash-data.s3.amazonaws.com/hymenoptera_data.zip", 'data/')

# 2. Load the model from a checkpoint
model = ImageClassifier.load_from_checkpoint("https://flash-weights.s3.amazonaws.com/image_classification_model.pt")
# 3a. Predict what's on a few images! ants or bees?
predictions = model.predict([
    "data/hymenoptera_data/val/bees/65038344_52a45d090d.jpg",
    "data/hymenoptera_data/val/bees/590318879_68cf112861.jpg",
    "data/hymenoptera_data/val/ants/540543309_ddbb193ee5.jpg",
])
print(predictions)
# 3b. Or generate predictions with a whole folder!
datamodule = ImageClassificationData.from_folder(folder="data/hymenoptera_data/predict/")
predictions = Trainer().predict(model, datamodule=datamodule)
# expect [1, 1, 0]
print(predictions)

推論にFlashタスクを使用するには、以下のようにします。
チェックポイント（チェックポイントとは、モデルが使用するすべてのパラメータの正確な値をキャプチャするファイルのことです）を使用して、事前に訓練された重みでタスクを初期化します。ローカルファイルでもリモートURLでも構いません。
データを flash.core.model.Task.predict() に渡します。
例えば、ImageClassifier は、ImageNet データセット上で 10 クラスの動物を分類するように訓練されています。事前に学習した重みを使用して、Hymenoptera データセットを使用して、画像とアリかハチかを予測することができます。この例では、チェックポイントから事前学習されたモデルをロードし、Hymenopteraデータセットを使用して予測を行います。

