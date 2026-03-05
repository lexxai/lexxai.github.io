---
layout: post
title: "Задача для зображень \"grey to rgb\" у моделі #keras"
date: 2024-03-07 15:43:00 +0000
tags: ["image", "keras", "TensorFlow", "tf"]
blogger_orig_link: https://lexxai.blogspot.com/2024/03/grey-to-rgb-keras.html
---

Ось чому не можна використати
[FC](https://medium.com/@vaibhav1403/fully-connected-layer-f13275337c7c)
з активатором "[ReLU](https://uk.wikipedia.org/wiki/ReLU)" для  цієї задачі: 

```
layers.Dense(3, activation="relu", name="gray_rgb", input_shape=(32,32,1))
```

[![](/assets/images/blog/32cbe543c61216f8-08e9ac96e3131f9b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivXDfavl9dYjsqpCmNE4w09u0Js-b3C9lr2ApaN0uTOwNe_ycuCnc72Jm4hP9liJZKDt9spoqY6UvU67o_1xuoTdFgmPHJNb96-dTYeDwO8Z7EJT_tTUBONoN0vSoZ0PL6MD4GqYouRZ0jHrsU491xDJ7qD8c0Px4xcXR8gXHK3XRK-uJ1IB9ypEe7bdHE/s2092/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-03-07%20%D0%BE%2016.52.06.png)  
*FC, relu*

Найкраще зробити підготовку dataset:

```
tx = np.repeat(x, 3, axis=-1)
```

або

```
tx = np.tile(x, (1, 1, 3))
```

Або шар
[Lambda](https://keras.io/api/layers/core_layers/lambda/)
(але я питання по збереження моделі до файлу):   

```
layers.Lambda(lambda x: tf.repeat(x, 3, axis=-1))
```

[![](/assets/images/blog/6cd4bf6d9a3f30b4-366cb14442dc53a3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipsDH0YjBa5QgQ-oT-zp_rDupMzln31zx5D7syJftP452sGweIbEbPr5PQnE-4tDu780gslysgItVkRYkg-P3kz0Z2939P3i5r1JafuohLDrogot4TYtez2zmYVO3KBDRQ-o0jAgRMeNOWnC4w8E5LU9W3eVT8iUux5eV6YDeTHI1ZljTfHGyMV9Y0XzRy/s963/img-rgb-set.png)  
*Grey to RGB*

```
rows = 4
plt.figure(figsize=(10,3*rows))
cols = rgb_images_train.shape[-1]
total = cols * rows
labels = ["R","G","B"]
for i in range(total):
    plt.subplot(rows,cols,i+1)
    plt.xticks([])
    plt.yticks([])
    plt.grid(False)
    id = i % cols
    rid = i // cols
    plt.imshow(rgb_images_train[0+rid,:,:,id], cmap=plt.cm.binary)
    plt.ylabel(f"Image {rid}, label: {np.argmax(y_train[rid])}")
    plt.xlabel(f"chanel {id} : '{labels[id]}'")
plt.show()
```

Або вже шар [Conv2D](https://keras.io/api/layers/convolution_layers/convolution2d/):

```
layers.Conv2D(3, (1, 1), use_bias=False, padding="same", kernel_initializer="ones", name="conv2d_108", input_shape=(32,32,1))
```

[![](/assets/images/blog/04ef88189b33f221-390474c2c14edb4c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihpLcyYIGAtPnVkq2Q4rPRLMIYE36-NAbf27SWl5Uf7j8vku8cBR9rKlBRzxksNiXON4dQpjNX67YMKcikVyeya7zV2uWeWLtnXdbBfwuHrLpCdPxD0dA5rEyS5W_VMvZqONJZ8wH4ThQjlKwWdK6wMXoLCRI9C8KoDRorL-6ea_bWX7iVA8z3NnWgNtZB/s2674/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-03-07%20%D0%BE%2017.17.50.png)  
*Conv2D 1х1*

```
activation_model = Model(inputs=model.input, 
                         outputs=[layer.output for layer in model.layers])

activations = activation_model.predict(x_test[0].reshape(1, 32, 32, 1))

for layer_index, layer_activation in enumerate(activations):
    print(f"{layer_index=}, {layer_activation.shape=}")
    if len(layer_activation.shape) == 4:  
        num_features = layer_activation.shape[-1]
        size = layer_activation.shape[1]

        rows = num_features // 1  
        cols = layer_activation.shape[-1]

        plt.figure(figsize=(16, 12))
        for i in range(num_features):
            plt.subplot(rows, cols, i + 1)
            img = layer_activation[0, :, :, i]
            plt.imshow(img, cmap='viridis')
            plt.axis('off')
            print("min:", np.min(img), "max",np.max(img))
        plt.tight_layout()
        plt.subplots_adjust(top=0.94)
        plt.suptitle(f'Layer {activation_model.layers[layer_index+1].name} Feature Maps')
        plt.show()
```
