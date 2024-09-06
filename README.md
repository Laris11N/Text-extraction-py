# Text-extraction-py

## Projeto: Extração de Texto

### Descrição
Este projeto tem como objetivo realizar a extração de texto em imagens utilizando técnicas de Reconhecimento Óptico de Caracteres (OCR). Ao processar as imagens fornecidas, é possível extrair o texto contido nelas e gerar uma saída digital.

### Estrutura do Repositório
- **inputs/**: Pasta onde estão armazenadas as imagens utilizadas para o reconhecimento de texto.
- **output/**: Pasta contendo os resultados dos textos extraídos das imagens.

### Tecnologias Utilizadas
- **Linguagem de Programação**: Python
- **Bibliotecas**: Tesseract OCR, pdf2image, glob, shutil, os

### Processo
1. As imagens foram capturadas de uma pasta específica.
2. A técnica de OCR foi aplicada para extrair o texto das imagens.
3. Os textos extraídos foram armazenados na pasta `output` como arquivos `.txt`.

### Como Funciona
- O script processa arquivos PDF convertendo-os em imagens.
- Cada imagem é enviada para o motor OCR do Tesseract para extração do texto.
- O texto extraído é salvo como um arquivo `.txt`, e os PDFs processados são movidos para uma pasta específica.

### Insights
- Aprendi a gerenciar dinamicamente pastas e automatizar a conversão de arquivos PDF para texto.
- Utilizar a configuração de idioma do Tesseract ajuda a melhorar a precisão da extração de texto para documentos em português.

### Próximos Passos
1. Explorar o uso de outras bibliotecas ou ferramentas de OCR.
2. Experimentar diferentes técnicas de pré-processamento de imagens para melhorar a precisão da extração de texto.
3. Adicionar mais funcionalidades, como o suporte a diferentes formatos de imagem.

### Código Usado

```python
import os
import shutil
from pdf2image import convert_from_path
import pytesseract
import glob

# Configuração do Tesseract (defina o caminho correto para o executável)
pytesseract.pytesseract.tesseract_cmd = r'C:\Program Files\Tesseract-OCR\tesseract.exe'

# Adicione o caminho para o Poppler no ambiente PATH
os.environ["PATH"] += os.pathsep + r'D:/Poppler/poppler-23.08.0/Library/bin'

# Pasta onde os PDFs originais estão localizados
pasta_origem = 'C:\BootCampy'

# Pasta onde os arquivos de texto processados serão salvos
pasta_texto_processado = 'C:\BootCampy\TXT'

# Pasta onde os PDFs convertidos serão temporariamente armazenados
pasta_pdf_convertido = 'C:\BootCampy\pdf_convertido'

# Verifique se a pasta de destino existe, se não, crie-a
if not os.path.exists(pasta_texto_processado):
    os.makedirs(pasta_texto_processado)

if not os.path.exists(pasta_pdf_convertido):
    os.makedirs(pasta_pdf_convertido)

# Lista de arquivos PDF na pasta de origem
arquivos = glob.glob(os.path.join(pasta_origem, '*.pdf'))

# Loop pelos arquivos PDF na lista
for caminho_origem in arquivos:
    print(f"Processando o arquivo PDF: {os.path.basename(caminho_origem)}")

    # Inicializar uma string para armazenar o texto do arquivo
    texto_arquivo = ''

    try:
        # Converta o PDF em imagens
        imagens = convert_from_path(caminho_origem)

        # Extraia o texto de cada imagem usando o Tesseract com a linguagem definida como português
        for imagem in imagens:
            texto_imagem = pytesseract.image_to_string(imagem, lang='por')
            if texto_imagem:
                texto_arquivo += texto_imagem

        # Verificar se o texto foi extraído
        if texto_arquivo:
            # Caminho completo para o arquivo de texto na pasta texto processado
            caminho_texto_destino = os.path.join(pasta_texto_processado, os.path.splitext(os.path.basename(caminho_origem))[0] + '.txt')

            # Salvar o texto em um arquivo TXT com o mesmo nome do arquivo PDF
            with open(caminho_texto_destino, 'w', encoding='utf-8') as arquivo_texto:
                arquivo_texto.write(texto_arquivo)

            # Mover o PDF original para a pasta "pdf_convertido"
            caminho_pdf_convertido = os.path.join(pasta_pdf_convertido, os.path.basename(caminho_origem))
            shutil.move(caminho_origem, caminho_pdf_convertido)

    except Exception as e:
        print(f"Erro ao processar o arquivo PDF '{os.path.basename(caminho_origem)}': {str(e)}")

# Move os PDFs convertidos de volta para a pasta de origem
pdfs_convertidos = glob.glob(os.path.join(pasta_pdf_convertido, '*.pdf'))
for caminho_pdf_convertido in pdfs_convertidos:
    shutil.move(caminho_pdf_convertido, os.path.join(pasta_origem, os.path.basename(caminho_pdf_convertido)))

print("Conversão de PDF para TXT concluída com sucesso.")
