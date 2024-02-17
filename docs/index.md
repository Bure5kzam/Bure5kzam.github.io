---
title: "Splash Page"
layout: splash
date: 2016-03-23T11:48:41-04:00
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/unsplash-image-1.jpg
  # actions:
  #   - label: "Download"
  #   - url: "https://github.com/mmistakes/minimal-mistakes/"
#   caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
excerpt: 로봇 개발하고 있습니다.
intro: 
  - excerpt: '컴퓨터 공학과 학사 졸 제조업 연구직 근무 중'
feature_row:

  - image_path: assets/image/vscode.png
  # - image_path: assets/image/mm-customizable-feature.png
    image_caption: "작성 중"
    alt: "vscode 사용하기"
    title: "vscode 사용하기"
    excerpt: "visual studio 에서 vscode로 넘어오는 팀원들을 위한 글"
    url: "/vscode"
    btn_label: "Read"
    btn_class: "btn--primary"

  - image_path: /assets/image/ubuntu.png
    # image_caption: "Image courtesy of [Unsplash](https://unsplash.com/)"
    alt: "리눅스 마스터 시험 준비하기"
    title: "리눅스 마스터 시험 준비하기"
    excerpt: "시험 준비하면서 공부 했던 내용 기록"
    url: "/linux-master"
    btn_label: "Read"
    btn_class: "btn--primary"

  # - image_path: /assets/image/mm-customizable-feature.png
  #   title: "Placeholder"
  #   excerpt: "This is some sample content that goes here with **Markdown** formatting."

# feature_row2:
#   - image_path: /assets/image/vscode.png
#     alt: "vscode"
#     title: "vscode 사용하기"
#     excerpt: vscode 고수돼서 생산성 향상시키기
#     url: "#test-link"
#     btn_label: "Read More"
#     btn_class: "btn--primary"
# feature_row3:
#   - image_path: /assets/images/unsplash-gallery-image-2-th.jpg
#     alt: "placeholder image 2"
#     title: "Placeholder Image Right Aligned"
#     excerpt: 'This is some sample content that goes here with **Markdown** formatting. Right aligned with `type="right"`'
#     url: "#test-link"
#     btn_label: "Read More"
#     btn_class: "btn--primary"
# feature_row4:
#   - image_path: /assets/images/unsplash-gallery-image-2-th.jpg
#     alt: "placeholder image 2"
#     title: "Placeholder Image Center Aligned"
#     excerpt: 'This is some sample content that goes here with **Markdown** formatting. Centered with `type="center"`'
#     url: "#test-link"
#     btn_label: "Read More"
#     btn_class: "btn--primary"
---

{% include feature_row id="intro" type="center" %}

{% include feature_row %}

<!-- {% include feature_row id="feature_row2" type="left" %} -->

<!-- {% include feature_row id="feature_row3" type="right" %} -->

<!-- {% include feature_row id="feature_row4" type="center" %} -->