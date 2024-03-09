# Japanese OCR Evaluation
日本語の文章画像に対するOCRの性能を評価しました。  
検証したOCRの方法は、[Google DocumentAI](https://cloud.google.com/document-ai?hl=ja), [OpenAI GPT-4V](https://platform.openai.com/docs/guides/vision), [Anthropic Claude3](https://docs.anthropic.com/claude/docs/vision), [OSS EasyOCR](https://github.com/JaidedAI/EasyOCR)の4つです。  
評価する日本語の文章は、[青空文庫から夏目漱石の「こころ」](https://www.aozora.gr.jp/cards/000148/card773.html)を利用しました。

具体的な方法、結果についてはnotesディレクトリ以下を参照してください。

### 検証の方法
検証した日付は2024/3/8のものであり、実行した時期や対象となる日本語の文章によって異なる結果が得られると思われます。
- DocumentAI: プロセッサとして「Document OCR」を利用
- GPT-4V: modelとして「gpt-4-vision-preview」を利用
- Claude3: modelとして「claude-3-opus-20240229」を利用
- EasyOCR: バージョン「1.7.1」を利用


### ルビあり日本語の文章
| DocumentAI | GPT-4V | Claude3 | EasyOCR |
| :---: | :---:| :---: | :---: |
| △   | x(リクエスト拒否) | x | x |

LLMを利用した動機は、ルビを`<>`で囲んで出力するように出力してくれることを期待してのことでしたが、Claude3はそもそも文字を認識できていないようでした。  

GPT-4Vにいたっては、`I'm sorry, I can't help with that request.`としてリクエストを拒否されて処理ができませんでした。理由は明らかではないですが、OpenAI側が意図を持って微調整している可能性もあります。

### ルビなしの日本語の文章
| DocumentAI | Claude3 | EasyOCR |
| :---: | :---:| :---: |
| ◎  | x | x |

DocumentAIは完璧に近い精度でOCRできていました。  
EasyOCRがある程度の精度で認識できていれば、EasyOCR -> GPT4による校正みたいな流れも想定していたので、残念。

### ルビを削除する方法に関して
OpenCVを利用することで、ルビつきの日本文章画像からルビを削除することができました。  
形態学的変換(`cv2.dilate`)と輪郭検出(`cv2.findContours`)を使用して膨張処理された画像から、ルビ以外の文字シークエンスの輪郭を検出し、その輪郭の横にあるものをルビとして認識して、白く塗り潰すような処理を行いました。