# TEXT2Verilog

# Summary

This repository explores the task of **generating synthesizable Verilog code from natural language specifications**. The project aims to automate the process of transforming textual hardware requirements into Verilog modules, thereby reducing development time and the likelihood of design errors.  

The work focuses on the use of **large language models (LLMs)** and **parameter-efficient fine-tuning methods** such as LoRA and QLoRA to adapt general-purpose models for hardware design tasks. A specialized dataset of Verilog code examples of varying complexity was collected, including both human-curated and machine-generated samples, to ensure the reliability and diversity of training data.  

Evaluation was performed using **VerilogEval benchmarks** and custom testbenches to verify not only syntactic correctness but also the functional validity of generated code. The results demonstrate that LLM-based approaches can achieve competitive accuracy and efficiency in Verilog generation, highlighting their potential for **AI-driven computer-aided design (CAD) tools** and future digital circuit development workflows.  

## Repository Contents
The repository provides a comprehensive set of resources for reproducing and extending the project, including:
- **Data collection scripts**: tools for extracting and preparing datasets used for model training.  
- **Data preprocessing modules**: scripts for structuring datasets into formats suitable for LLM fine-tuning.  
- **Model fine-tuning notebooks**: Jupyter notebooks for adapting LLMs (LoRA/QLoRA) to Verilog generation tasks.  
- **Validation scripts**: evaluation pipelines to assess the accuracy and synthesizability of generated Verilog code.  
- **Usage examples**: notebooks demonstrating how to generate Verilog modules from natural language descriptions.  
- **Performance tests**: benchmarks for comparing different models on Verilog generation tasks.  

---

# Описание Репозитория

Этот репозиторий представляет собой комплексное хранилище, содержащее все необходимые ресурсы для реализации проекта по генерации синтезируемого Verilog-кода из спецификаций на естественном языке. В репозитории вы найдете:

- **Скрипты для сбора данных**: Коды, предназначенные для извлечения и подготовки начальных данных, необходимых для обучения моделей.
- **Модули обработки данных**: Скрипты, используемые для предварительной обработки и структурирования данных в формате, оптимальном для последующей обработки моделями.
- **Ноутбуки дообучения моделей**: Jupyter ноутбуки, содержащие код для тонкой настройки больших языковых моделей на специализированных датасетах.
- **Скрипты для валидации**: Инструменты для проверки качества генерируемого кода, включая методы оценки точности и корректности синтезируемых Verilog-модулей.
- **Ноутбуки с примерами использования моделей**: Примеры кода, демонстрирующие, как использовать дообученные модели для генерации Verilog-кода из текстовых описаний.
- **Тесты производительности моделей**: Скрипты для тестирования и сравнения производительности различных моделей на задачах генерации кода.

# Требования к ресурсам и библиотекам

| Библиотека/Ресурс   | Версия/Требование  |
|---------------------|-------------------|
| Pytorch             | 2.2.0             |
| CUDA                | 8.6               |
| CUDA Toolkit        | 12.1              |
| Xformers            | 0.0.25            |
| Память GPU          | 16 ГБ             |

## Использование библиотеки Unsloth

Для оптимизации настройки и минимизации потребления ресурсов, в проекте применяется библиотека [Unsloth](https://github.com/unslothai/unsloth). Подробные инструкции по установке доступны по [ссылке на GitHub](https://github.com/unslothai/unsloth).

## Запуск модели

Модели адаптеров LoRA, дообученные для генерации Verilog-кодов, доступны для скачивания на платформе Hugging Face. Вы можете найти их по следующей ссылке: [Driseri на HuggingFace](https://huggingface.co/Driseri).

### Пример кода для использования модели

Для запуска модели используйте следующий код:

```python
from unsloth import FastLanguageModel

# Загрузка модели и токенизатора
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="Driseri/lora3_newData_50step_r128",  # Укажите название вашей модели
    max_seq_length=512,       # Максимальная длина последовательности
    dtype='fp16',             # Тип данных, например 'fp16' для снижения потребления памяти
    load_in_4bit=True         # Загрузка модели в 4-битном формате для уменьшения потребления памяти
)

# Включение режима более быстрого вывода
FastLanguageModel.for_inference(model)

# Токенизация входных данных
inputs = tokenizer(
    [
        "Введите ваш Verilog запрос здесь"
    ], 
    return_tensors="pt"
).to("cuda")

# Генерация вывода модели
outputs = model.generate(**inputs, max_new_tokens=64, use_cache=True)
print(tokenizer.batch_decode(outputs))
```

## Альтернативный способ использования модели

Вы также можете использовать `AutoModelForPeftCausalLM` от Hugging Face, если у вас не установлена библиотека Unsloth. Следует отметить, что этот метод может быть крайне медленным, так как поддержка загрузки моделей в 4-битном формате отсутствует и инференс в Unsloth происходит в два раза быстрее.

```python

from peft import AutoPeftModelForCausalLM
from transformers import AutoTokenizer

# Загрузка модели и токенизатора без Unsloth
model = AutoPeftModelForCausalLM.from_pretrained(
    "lora_model",  # Указать модель
    load_in_4bit=False  # Загрузка в 4-битном формате не поддерживается
)
tokenizer = AutoTokenizer.from_pretrained("lora_model")
```

## Структура проекта

Проект организован в несколько основных папок, каждая из которых содержит файлы и скрипты, необходимые для различных этапов обработки и анализа данных.

### Основные папки:

- **Verilog-eval-progs**
  - Содержит валидационные датасеты для проверки генерированного Verilog-кода.
  
- **Vgen**
  - Хранит запросы для языковой модели для генерации Verilog-кода.

- **generate_verilog**
  - Примеры сгенерированных модулей Verilog разными дообученными моделями.

- **hdlbits-solutions**
  - Решения с сайта HDLBits, используемые для сравнения и анализа.

- **notebooks**
  - Готовые примеры Jupyter ноутбуков с кодами дообучения и валидации моделей.

- **scripts**
  - Разнообразные скрипты для обработки и анализа данных.

- **testbench**
  - Тестовые бенчмарки для проверки функциональности Verilog-модулей.

- **validate_results**
  - Датасеты результатов валидации программ Verilog, сгенерированных разными моделями.


