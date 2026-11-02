# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
- `--trace` で見えたツール呼び出し（ツール名と引数）：
- 1つの質問での LLM 呼び出し回数（`requests`）：
- 追加したツールと、docstring に書いた説明：

## 宿題

- 取り組んだレベル：基礎 
- やったこと：docstring の「用語を1つだけ入れる」を「まとめて入れる」に変えると、検
索の回数がどう変わる
- 結果：
変更前

@momomomomomonga ➜ /workspaces/lecture-ai-engineering-2026 (lec03) $ uv run python -m app.agent --trace "LoRA とQLoRAの違いは？ "
[model] gemini-3.5-flash-lite
LoRAとQLoRAの主な違いは、**ベースモデルの重みの保持方法と量子化の有無**にあります。

* **LoRA**: 事前学習済みモデルの重みをそのまま（通常は16ビットなどの精度で）固定し、低ランク行列（追加のパラメータ）のみを学習して効率化を図る手法です。
* **QLoRA**: ベースモデルの重みを4ビットなどに**量子化**（圧縮）してメモリ使用量を大幅に削減した状態で固定し、その上でLoRAによる学習を行う手法です。これにより、より大きなモデルを限られたGPUメモリで効率よく学習できるようになります。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008119'), details={'text_prompt_tokens': 1198}, input_text_tokens=1198, input_tokens=1198, output_tokens=181, requests=2, tool_calls=2)


変更後

@momomomomomonga ➜ /workspaces/lecture-ai-engineering-2026 (lec03) $  uv run python -m app.agent --trace "LoRA とQLoRAの違いは？ "
[model] gemini-3.5-flash-lite
LoRAとQLoRAの主な違いは、**ベースモデルの重みをそのまま使うか、量子化（軽量化）して使うか**という点にあります。

* **LoRA（Low-Rank Adaptation）**
  事前学習済みの重みを固定したまま、追加の低ランク行列（アダプタ）だけを学習して効率的にファインチューニングを行う手法です。
* **QLoRA（Quantized LoRA）**
  LoRAの仕組みに**量子化（Quantization）**を組み合わせた手法です。ベースモデルの重みを4ビットなどの低精度に量子化してメモリ消費を大幅に抑えつつ、LoRAによって学習を行います。これにより、限られたGPUメモリでもより大きなモデルを扱うことが可能になります。

関係する回：第2回

--- trace ---
[tool call]   search_glossary({'query': 'LoRA QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[usage] RunUsage(cost=Decimal('0.0007461'), details={'text_prompt_tokens': 887}, input_text_tokens=887, input_tokens=887, output_tokens=192, requests=2, tool_calls=1)


- 気づき・疑問：
変更前はLoRAとQLoRAを個別に検索していたが、変更後は1回の検索で両方を取得する方式に変更されていた。その結果、ツール呼び出し回数や入力トークン数、実行コストが削減されている。また、回答も「違い」を最初に説明する構成になり、質問意図に沿った分かりやすい回答になっていると感じた。一方で、検索をまとめることで情報の網羅性や回答精度が低下しないのか、またトレースではLoRAしか取得されていないように見えるにもかかわらずQLoRAの説明ができている理由が気になった。

