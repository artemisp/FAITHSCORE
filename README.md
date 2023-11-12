# FaithScore
[![made-with-python](https://img.shields.io/badge/Made%20with-Python-red.svg)](#python)
[![arxiv](https://img.shields.io/badge/arXiv-2311.01477-b31b1b.svg)](https://arxiv.org/abs/2311.01477)
[![PyPI version faithscore](https://badge.fury.io/py/faithscore.svg)](https://pypi.python.org/pypi/faithscore/)
[![Downloads](https://pepy.tech/badge/faithscore)](https://pepy.tech/project/faithscore)

This is the official release accompanying our paper, [FAITHSCORE: Evaluating Hallucinations in Large Vision-Language Models](https://arxiv.org/abs/2311.01477). FAITHSCORE is available as a PIP package as well.

If you find FAITHSCORE useful, please cite:
```
@misc{faithscore,
      title={FAITHSCORE: Evaluating Hallucinations in Large Vision-Language Models}, 
      author={Liqiang Jing and Ruosen Li and Yunmo Chen and Mengzhao Jia and Xinya Du},
      year={2023},
      eprint={2311.01477},
      archivePrefix={arXiv},
      primaryClass={cs.CV}
}
```

## Process
![FAITHSCORE process](faithscore.png)


## Install

1. Install [LLaVA 1.5](https://github.com/haotian-liu/LLaVA). 
   
   Note that you don't need to download parameter weights of LLaVA 1.5 if you use OFA to realize fact verification. 
2. Install [modelscope](https://modelscope.cn/home);
   ```python
   pip install modelscope
   pip install "modelscope[multi-modal]" 
   ```
3. Install our package.
    ```python
    pip install faithscore==0.0.9
    ```

## Running FaithScore using Pip Package
You can evaluate answers generated by large vision-language models via our metric. 

```python
from faithscore.framework import FaithScore

images = ["./COCO_val2014_000000164255.jpg"]
answers = ["The main object in the image is a colorful beach umbrella."]

scorer = FaithScore(vem_type="...", api_key="...", llava_path=".../llava/eval/checkpoints/llava-v1.5-13b", use_llama=False,
                   llama_path="...llama2/llama-2-7b-hf")
score, sentence_score = scorer.faithscore(answers, images)
```

Parameters for FaithScore class:
- `vem_type`: You can set this parameter as `ofa-ve`, `ofa`, or `llava`. This parameter decides which model is used to do fact verification. 
- `api_key`: OpenAI API Key.
- `llava_path`: The model folder for LLaVA 1.5. If you don't set `vem_type` as `llava`, you needn't to set it.
- `use_llama`: Whether use llave to achieve sub-sentence identification. If it is False, the code uses ChatGPT for this stage. 
- `llama_path`: The model folder for LLaMA 2 7B. Before using it, please convert the model weight into huggingface format. For details, you can refer to [this link](https://huggingface.co/docs/transformers/model_doc/llama2). If you don't use LLaMA, skip this parameter.

## Running FaithScore using a Command Line
You can also evaluate answers generated by the following command line.

```python
python run.py --answer_path {answer_path} --openai_key {openai_key} --vem_type {vem_type} --llava_path {llava_path} --llama_path {llama_path} --use_llama {use_llama}
```
Parameters:
- `--answer_path`:  The answer file can be something like test.jsonl. It should be a .jsonl format where each line is a dict {"answer": "xxx", "image": "xxxx"} that contains answer generated by LVLMs and image path.
- `--openai_key`: OpenAI API Key.
- `--llava_path`: The model folder for LLaVA 1.5. If you don't set `vem_type` as `llava`, you needn't to set it.
- `--use_llama`: Whether use llave to achieve sub-sentence identification. If it is False, the code uses ChatGPT for this stage. 
- `--llama_path`: The model folder for LLaMA 2 7B. Before using it, please convert the model weight into huggingface format. For details, you can refer to [this link](https://huggingface.co/docs/transformers/model_doc/llama2). If you don't use LLaMA, skip this parameter.

## Data
### Annotation Data
The data is given in a json format file. For example, 
```python
{"id": "000000525439", "answer": "The skateboard is positioned on a ramp, with the skateboarder standing on it.", "stage 1": {"The skateboard is positioned on a ramp": 1, " with the skateboarder standing on it": 1}, "stage 2": {"There is a skateboard.": 1, "There is a ramp.": 0, "There is a skateboarder.": 1, "The skateboarder is standing on a skateboard.": 0}}
```
Dta Format:
- `id`:  question_id. You can get the corresponding image name in the COCO validation dataset (2014) by this format `COCO_val2014_{id}.jpg`. 
- `answer`: answer generated by the Large Vision-Language Model.
- `stage 1`: All the sub-sentences and their corresponding labels}. `1` denotes analytical sub-sentence.
- `stage 2`: All the atomic facts and their corresponding labels}. `1` denotes the atomic fact contains hallucination.

You can download our [annotation dataset](https://github.com/bcdnlp/FAITHSCORE/blob/main/annotation.jsonl).



### Automatic Evaluation Data
You can download our [automatic evaluation datasets](https://drive.google.com/drive/folders/10RyGYrtEdxFC2u8wE_ojeSDslN7CQ4OM?usp=drive_link).

