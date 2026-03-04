---
layout: post
title: "Завершив курс Convolutional Neural Networks in TensorFlow (DeepLearning.AI TensorFlow Developer)"
date: 2023-07-15 02:38:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2023/07/convolutional-neural-networks-in.html
---

Нова програма навчання від GwG UA з вивчення курсів
[DeepLearning.AI TensorFlow Developer](https://www.coursera.org/professional-certificates/tensorflow-in-practice)
на платформі [Coursera](https://www.coursera.org/).
З циклу
[ML Bootcamp від Google](https://rsvp.withgoogle.com/events/ml-bootcamp-ukraine/home).

[![](/assets/images/blog/d923b562b3e02f97-90b114bba597a153.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOIvZttgGP9EwCYH05qxn6Vk2IVRLU3D9lsflmuH9iWfULZI4T4STl7_rTONxuKMoLxQQNJgwyv4rvwLOvWSYA-DjuwhsNUBmqHeO5JerWMw8uOLMoVismfhG3tvDVoRby_bd3gFbvZZJ7un5mBFO4ROFnSBgE3TRxK6iePwWlkC54UhPzrEeP7eCCRlUq/s1100/Coursera-Convolutional%20Neural%20Networks%20in%20TensorFlow-clear.png)  
*Convolutional Neural Networks in TensorFlow, Липень, 2023*

  

Перший курс вступний у світ TensorFlow:
[Introduction to TensorFlow for Artificial Intelligence, Machine Learning,
and Deep Learning](https://www.coursera.org/learn/introduction-tensorflow/home/welcome)

  

Остання завдання дало жару, коли коментар shape 1 очікується, а модель працює тільки з shape 26 для lables. =sparse\_categorical\_crossentropy

tf.keras.layers.Dense(26, activation='softmax')

])

model.compile(optimizer = tf.keras.optimizers.RMSprop(learning\_rate=0.001),

loss = 'sparse\_categorical\_crossentropy',

metrics=['accuracy'])

### About this Course

If you are a software developer who wants to build scalable AI-powered algorithms, you need to understand how to use the tools to build them. This course is part of the upcoming Machine Learning in Tensorflow Specialization and will teach you best practices for using TensorFlow, a popular open-source framework for machine learning.  
  
In Course 2 of the deeplearning.ai TensorFlow Specialization, you will learn advanced techniques to improve the computer vision model you built in Course 1. You will explore how to work with real-world images in different shapes and sizes, visualize the journey of an image through convolutions to understand how a computer “sees” information, plot loss and accuracy, and explore strategies to prevent overfitting, including augmentation and dropout. Finally, Course 2 will introduce you to transfer learning and how learned features can be extracted from models.   
  
The Machine Learning course and Deep Learning Specialization from Andrew Ng teach the most important and foundational principles of Machine Learning and Deep Learning. This new deeplearning.ai TensorFlow Specialization teaches you how to use TensorFlow to implement those principles so that you can start building and applying scalable models to real-world problems. To develop a deeper understanding of how neural networks work, we recommend that you take the Deep Learning Specialization.

*Learning Objectives*Learning Objectives

* Gain understanding about Keras’ utilities for pre-processing image data, in particular the ImageDataGenerator class
* Develop helper functions to move files around the filesystem so that they can be fed to the ImageDataGenerator
* Learn how to plot training and validation accuracies to evaluate model performance
* Build a classifier using convolutional neural networks for performing cats vs dogs classification

[![](/assets/images/blog/b5adfd54bc677365-5a39aa4b032dfcaf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcvoVKNoJ9ccdHLItTpaM5TPDUBI-OnDeRFbXx8u7fH_Ed0WkO_mZxWl7IsvdpYqkIFbt6hWjfP2TIlkKs2vpCU7dIcWI-ba0lyz-43lO4pDOi9bCOBUxBGpPPDaYGlfZJCO-hAClPJgb8UJkiS5rSfoLCyloDGNcp5L0DDdnOA0bJJ40ACIUUg2VfQ2iT/s1316/Screenshot%202023-07-15%20190019.png)  
*Grades*

P.S. Допомагає перекладати субтитри мій новий плагін для браузера
[CST Extension: Real-Time Subtitle Translation for Coursera Videos.](https://github.com/lexxai/coursera-subtitle-translate-extension)
Котрий є у
[Chrome Store](https://chrome.google.com/webstore/detail/coursera-subtitle-transla/dflliggaoffmbngkophcejkhkllaebjn).
