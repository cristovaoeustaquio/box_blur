# Filtro de Desfoque

Um filtro de desfoque (também conhecido como filtro de suavização ou blur) é uma técnica de processamento de imagem que suaviza as transições entre cores e reduz o ruído na imagem. Isso é realizado através da média dos valores dos pixels vizinhos em uma determinada área em torno de cada pixel da imagem. O resultado é uma imagem mais suave e com menos detalhes de alta frequência.

Esse tipo de filtragem tem várias aplicações em processamento de imagens, como remoção de ruído de alta frequência, conforme ilustrado na figura abaixo.

![image](https://user-images.githubusercontent.com/10417566/229540522-bb4017a4-c391-4f8c-b91d-d202f10a3ef9.png)

Para aplicar um filtro de desfoque (especificamente o filtro _box blur_), primeiro defina a dimensão do filtro, geralmente representada por um número ímpar (como 3x3, 5x5 etc.). Em seguida, percorra cada pixel da imagem e considere sua vizinhança, que é a área formada pelos pixels adjacentes conforme a dimensão do filtro escolhido. Calcule a média dos valores de intensidade desses pixels vizinhos e substitua o valor do pixel central por essa média. Repita esse processo para todos os pixels da imagem, tomando cuidado com as bordas, onde pode ser necessário aplicar um tratamento específico, como ignorar os pixels fora da imagem ou estender os valores das bordas. Caso a imagem seja colorida, realize o processo para cada um dos canais de cor da imagem. Após processar todos os pixels, a imagem resultante terá o efeito de suavização do filtro box blur aplicado.

A animação abaixo ilustra o processo de filtragem descrita. Nesse caso utiliza-se um filtro 3x3, $F$ é a imagem original e $G$ a imagem resultante.

![image](https://ai.stanford.edu/~syyeung/cvweb/gifs/moving%20average.gif)

### Passo a passo de como aplicar um filtro de desfoque em uma imagem em tons de cinza ou para cada canal de uma imagem colorida (RGB):

1. Escolha o tamanho do filtro de desfoque (por exemplo, 3x3, 4x4, 5x5 etc.). O tamanho do filtro determina o quão borrada será a imagem resultante.

2. Calcule o preenchimento (padding) necessário para o filtro. O preenchimento é igual à metade do tamanho do filtro arredondado para baixo. Por exemplo, para um filtro 5x5, o preenchimento é 2.

3. Crie uma nova imagem para armazenar o resultado. A imagem resultante deve ter as mesmas dimensões que a imagem original.

4. Percorra os pixels da imagem original, pulando os pixels das bordas. Faça isso usando dois loops aninhados, um para as linhas e outro para as colunas, começando e terminando com o valor de preenchimento.

5. Para cada pixel, calcule a média dos valores dos pixels na vizinhança do pixel atual, baseado no tamanho do filtro. Faça isso usando outros dois loops aninhados, um para as linhas do filtro e outro para as colunas do filtro. Some os valores dos pixels na vizinhança e divida a soma pelo número total de elementos no filtro (largura do filtro * altura do filtro).

6. Atribua o valor médio calculado ao pixel correspondente na imagem resultante.

7. Após percorrer todos os pixels da imagem original, copie os pixels das bordas da imagem original para a imagem resultante. Isso pode ser feito usando dois loops adicionais para percorrer as bordas da imagem e copiar os valores dos pixels.

8. Retorne a imagem resultante com o filtro de desfoque aplicado.


### Pseudo-código

``` pseudocode
function apply_box_blur(image, filter_size):
    // Get the dimensions of the input image
    width <- image.width
    height <- image.height

    // Create a new image to store the result
    result <- new image(width, height)

    // Calculate the padding size for the filter
    pad <- filter_size / 2

    // Loop through the image pixels, skipping the border pixels
    for row in range(pad, height - pad):
        for col in range(pad, width - pad):
            // Initialize the sum for the current pixel
            sum <- 0

            // Loop through the filter's rows and columns
            for k_row in range(-pad, pad + 1):
                for k_col in range(-pad, pad + 1):
                    // Add the corresponding image pixel value to the sum
                    sum <- sum + image[row + k_row][col + k_col]

            // Calculate the average value for the current pixel
            average <- sum / (filter_size * filter_size)

            // Assign the average value to the corresponding pixel in the result image
            result[row][col] <- average

    // Copy the border pixels from the input image to the result image
    for row in range(0, height):
        for col in range(0, pad):
            result[row][col] <- image[row][col]
            result[row][width - col - 1] <- image[row][width - col - 1]

    for col in range(0, width):
        for row in range(0, pad):
            result[row][col] <- image[row][col]
            result[height - row - 1][col] <- image[height - row - 1][col]

    // Return the blurred image
    return result
```
---
# Exercício Computacional III

Nesse exercício vocês irão utlizar o padrão produtor-consumidor para aplicar o filtro _box blur_ em um conjunto de arquivos utilizando múltiplas threads.

Inicialmente vocês devem implementar a função _apply_box_blur_ conforme descrito na seção anterior. Para a aula de hoje (04/04), vocês devem finalizar a implementação e testar com o conjunto de imagens disponíveis na pasta `input`. 

Na próxima terça feira (11/04) iremos modificar o código implementado para incluir:
* 1 thread produtora, responsável por monitorar arquivos existentes e novos arquivos na pasta `input` e incluir o nome do arquivo correspondente em um buffer de tamanho limitado
* $n$ threads consumidoras, responsáveis por aplicar o filtro _box blur_ em cada novo arquivo incluído no buffer e salvar o arquivo resultante no diretório `output`

Segue um diagrama ilustrando a solução a ser implementada:

![image](https://user-images.githubusercontent.com/10417566/230461230-e342e511-6fc6-4e13-9c1a-0958815cdef5.png)

Utilizem o codespaces para realizar o exercício.

