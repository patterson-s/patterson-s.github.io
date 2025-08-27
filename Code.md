---
layout: page
title: "Code"
permalink: /Code/
---
This page contains some examples of research pipelines I've developed for projects that are still in progress. 

**Audio Processing**
I've been working on a project that involves content analysis on podcasts. The pipeline for building a corpus from podcasts works in the following steps: 
1) [Parse RSS Feeds for podcast links](https://podcast-rss.streamlit.app/): the first step is to find the download links for the podcasts in an RSS feed. The attached tool queries some common podcast directories to find the link for an RSS feed. Once you've found one, you can click the "parse" button a bit further down to extract the links and metadata. You can also use the download button here, but it will take some time!
2) [Transcribe Audio Files](https://whispertranscriber-ypxjgklxlqn6zbmz6e9w8h.streamlit.app/): after downloading the podcasts, you can then transcribe them into text data. The website here does this for free with the OpenAI whisper transcription model (it's small enough to fit in a free-tier instance on streamlit). There is a batch transcription mode, but you can also just use this to transcribe yourself talking.

**Image Processing**
For another project, I've been working on making old documents machine readable. The Mistral OCR model works well for this. So far, the pipeline for this works in these steps: 
1) [Mistral OCR](https://mistral-ocr-gui.streamlit.app/): You can test out the Mistral OCR tool with this little interface. Note - you will need an API key for it to work.
2) [OCR Post-Correction]

- [State Detection NER Demo](https://patterson-s-state-detector-ner-app-vz4hx4.streamlit.app/) - This is a custom named entity detection (NER) model that can extract mentions of countries from unstructured text. Follow the link to test out the most recent version. 
- [Occupation Classifier](https://occupationclassifierfinetunegit-tgydnpdgrq3ddjrf5e8plp.streamlit.app/) - A fine-tuned GPT model for classifying occupations using BLS codes. Follow the link to try the most recent version. 
- [Audio transcription](https://whispertranscriber-ypxjgklxlqn6zbmz6e9w8h.streamlit.app/) - This illustrates how to transcribe audio files for free using the OpenAI whisper model. If working with many files or with large files, this [colab](https://colab.research.google.com/drive/10qucU8nBED9LA-aIZ8ViK7yR_4btqEnL?usp=sharing) version works better and allows for free-tier GPU acceleration.
- [Mistral OCR](https://mistral-ocr-gui.streamlit.app/) This is a simple interface to use the Mistral OCR tool. For more about Mistral OCR, see this [link](https://mistral.ai/fr/news/mistral-ocr)
- [Dynamic Prompt Developer](https://searchagent-patterson.streamlit.app/). Under development 
