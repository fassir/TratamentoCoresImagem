<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1F9BD4,50:2E75B6,100:16265F&height=200&section=header&text=TratamentoCoresImagem&fontSize=44&fontColor=FFFFFF&fontAlignY=38&desc=Processamento%20de%20Imagens%20Coloridas%20com%20Python%20e%20OpenCV&descAlignY=58&descSize=18&animation=fadeIn" width="100%"/>

[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)](https://opencv.org)
[![NumPy](https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white)](https://numpy.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)](https://jupyter.org)

[![OpenCV](https://img.shields.io/badge/OpenCV-4.8.0-5C3EE8?style=flat-square)]()
[![NumPy](https://img.shields.io/badge/NumPy-1.26.4-013243?style=flat-square)]()
[![Matplotlib](https://img.shields.io/badge/Matplotlib-3.8.4-11557C?style=flat-square)]()
[![Sem ML](https://img.shields.io/badge/abordagem-processamento%20clássico%20(sem%20ML)-2E75B6?style=flat-square)]()

</div>

---

## 📖 Sobre o Projeto

**TratamentoCoresImagem** é um projeto de **processamento clássico de imagens** com Python e OpenCV, sem uso de Machine Learning. O notebook demonstra um pipeline completo de manipulação de pixels: da leitura de uma foto colorida em JPEG até a geração de versões em **escala de cinza** e **preto e branco** com diferentes limiares (thresholds), passando pelo formato intermediário **PPM** (Portable Pixmap Format).

> 🎯 **Diferencial didático:** A implementação realiza as conversões de cor usando **álgebra matricial direta** (NumPy), sem depender das funções de alto nível do OpenCV — tornando o processo transparente e educativo.

---

## 🗂️ Estrutura do Projeto

```
TratamentoCoresImagem/
├── 📓 tratamento_de_imagem.ipynb    # Notebook principal
│
├── 📁 entrada/
│   ├── 🖼️ landscape.jpeg            # Imagem colorida de entrada
│   └── 🖼️ entrada.ppm               # Imagem convertida para PPM
│
├── 📁 saida/
│   ├── 🌫️ saida_cinza.ppm           # Saída em escala de cinza (PPM)
│   ├── 🌫️ saida_cinza.jpg           # Saída em escala de cinza (JPEG)
│   ├── ⬛ saida_pb_90.ppm            # Preto e branco — threshold 90
│   ├── ⬛ saida_pb_90.jpg            # Preto e branco — threshold 90
│   ├── ⬛ saida_pb_100.ppm           # Preto e branco — threshold 100
│   ├── ⬛ saida_pb_100.jpg           # Preto e branco — threshold 100
│   ├── ⬛ saida_pb_110.ppm           # Preto e branco — threshold 110
│   └── ⬛ saida_pb_110.jpg           # Preto e branco — threshold 110
│
└── 📄 README.md
```

---

## 🔄 Pipeline de Processamento

<div align="center">

```
┌─────────────────────────────────────────────────────────────────────┐
│                     PIPELINE DE PROCESSAMENTO                       │
│                                                                     │
│  ┌──────────────┐                                                   │
│  │ landscape    │                                                   │
│  │ .jpeg        │  → Leitura com OpenCV (BGR → RGB)                │
│  │ (colorida)   │                                                   │
│  └──────┬───────┘                                                   │
│         │                                                           │
│         ▼                                                           │
│  ┌──────────────┐                                                   │
│  │ entrada      │  → Conversão para formato PPM                    │
│  │ .ppm         │    (Portable PixMap — sem compressão)            │
│  └──────┬───────┘                                                   │
│         │                                                           │
│         ├──────────────────────────────────────────────┐           │
│         │                                              │           │
│         ▼                                              ▼           │
│  ┌──────────────┐                         ┌───────────────────┐   │
│  │ saida_cinza  │  Média Ponderada RGB    │  saida_pb_*.ppm/  │   │
│  │ .ppm / .jpg  │  0.299R+0.587G+0.114B  │  .jpg             │   │
│  └──────────────┘                         │  Thresholds:      │   │
│                                           │  90 / 100 / 110   │   │
│                                           └───────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

</div>

---

## 🎨 Conversões de Cor

### 1. RGB → Escala de Cinza (Média Ponderada)

<details>
<summary>🔢 Implementação Matricial</summary>

A conversão para escala de cinza utiliza a fórmula **luma (ITU-R BT.601)**, que pondera os canais RGB conforme a sensibilidade do olho humano:

```
Cinza = 0.299 × R  +  0.587 × G  +  0.114 × B
```

> 👁️ O olho humano é mais sensível ao verde (~59%), depois ao vermelho (~30%) e menos ao azul (~11%).

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

# --- Leitura da imagem ---
img_bgr = cv2.imread('landscape.jpeg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)  # OpenCV usa BGR por padrão

# --- Conversão para PPM intermediário ---
cv2.imwrite('entrada.ppm', img_bgr)

# --- Conversão para Escala de Cinza (manipulação matricial) ---
R = img_rgb[:, :, 0].astype(np.float64)
G = img_rgb[:, :, 1].astype(np.float64)
B = img_rgb[:, :, 2].astype(np.float64)

# Média ponderada — ITU-R BT.601
cinza = (0.299 * R) + (0.587 * G) + (0.114 * B)
cinza = cinza.astype(np.uint8)

# Salvar resultados
cv2.imwrite('saida_cinza.ppm', cinza)
cv2.imwrite('saida_cinza.jpg', cinza)

print(f"Shape da imagem original: {img_rgb.shape}")
print(f"Shape da imagem em cinza: {cinza.shape}")
```

</details>

---

### 2. Escala de Cinza → Preto e Branco (Threshold)

<details>
<summary>⬛ Binarização com Múltiplos Thresholds</summary>

```python
def aplicar_threshold(img_cinza: np.ndarray, threshold: int) -> np.ndarray:
    """
    Binariza uma imagem em escala de cinza.
    
    Pixels com valor > threshold → BRANCO (255)
    Pixels com valor ≤ threshold → PRETO  (0)
    
    Args:
        img_cinza: Array 2D com valores 0-255
        threshold: Limiar de binarização
    
    Returns:
        Imagem binarizada (0 ou 255)
    """
    img_pb = np.where(img_cinza > threshold, 255, 0).astype(np.uint8)
    return img_pb

# Aplicar três variações de threshold
thresholds = [90, 100, 110]

for t in thresholds:
    img_pb = aplicar_threshold(cinza, t)
    cv2.imwrite(f'saida_pb_{t}.ppm', img_pb)
    cv2.imwrite(f'saida_pb_{t}.jpg', img_pb)
    
    # Calcular proporção preto/branco
    total = img_pb.size
    brancos = np.sum(img_pb == 255)
    pretos  = np.sum(img_pb == 0)
    print(f"Threshold {t}: {brancos/total*100:.1f}% branco | {pretos/total*100:.1f}% preto")
```

**Efeito dos Thresholds:**

| Threshold | Pixels → Branco | Pixels → Preto | Resultado Visual |
|-----------|-----------------|----------------|-----------------|
| **90** | Pixels > 90 (maioria) | Pixels ≤ 90 (sombras) | Imagem mais clara, menos detalhes escuros |
| **100** | Equilíbrio moderado | Equilíbrio moderado | Balanço padrão |
| **110** | Menos pixels (tons médios excluídos) | Mais pixels → preto | Imagem mais escura, mais detalhes perdidos |

</details>

---

## 🔬 Formatos de Imagem Utilizados

<div align="center">

| Formato | Extensão | Compressão | Canais | Uso no Projeto |
|---------|----------|-----------|--------|----------------|
| **JPEG** | `.jpg` | Com perda (lossy) | RGB | Entrada original da paisagem |
| **PPM** | `.ppm` | Sem perda (lossless) | RGB / Grayscale | Formato intermediário e saídas |
| **JPEG** | `.jpg` | Com perda | Grayscale | Saídas otimizadas para visualização |

</div>

> 📌 O **formato PPM** (Portable Pixmap) armazena cada pixel como bytes brutos sem compressão, tornando-o ideal para processamento matricial direto e garantindo fidelidade total dos valores de pixel.

---

## 📊 Visualização Comparativa

<details>
<summary>📈 Código de Visualização Side-by-Side</summary>

```python
def visualizar_pipeline(original, cinza, pb_90, pb_100, pb_110):
    """Exibe todas as versões da imagem em um grid comparativo."""
    fig, axes = plt.subplots(1, 5, figsize=(22, 5))
    fig.suptitle('Pipeline de Processamento — TratamentoCoresImagem',
                 fontsize=14, fontweight='bold')

    dados = [
        (original,  'Original (RGB)',      None),
        (cinza,     'Escala de Cinza',     'gray'),
        (pb_90,     'Preto/Branco T=90',   'gray'),
        (pb_100,    'Preto/Branco T=100',  'gray'),
        (pb_110,    'Preto/Branco T=110',  'gray'),
    ]

    for ax, (img, titulo, cmap) in zip(axes, dados):
        ax.imshow(img, cmap=cmap)
        ax.set_title(titulo, fontsize=11)
        ax.axis('off')

    plt.tight_layout()
    plt.savefig('comparativo_pipeline.png', dpi=150, bbox_inches='tight')
    plt.show()

# Chamar após processar todas as versões
visualizar_pipeline(img_rgb, cinza, pb_90, pb_100, pb_110)
```

</details>

---

## 📦 Versões das Bibliotecas

<div align="center">

| Biblioteca | Versão | Função no Projeto |
|-----------|--------|-------------------|
| **OpenCV** | `4.8.0` | Leitura/escrita de imagens, conversão de espaços de cor |
| **NumPy** | `1.26.4` | Manipulação matricial dos arrays de pixels |
| **Matplotlib** | `3.8.4` | Visualização e comparação das imagens |
| **Jupyter** | `latest` | Ambiente interativo de desenvolvimento |

```bash
# Instalação com versões exatas
pip install opencv-python==4.8.0.76 numpy==1.26.4 matplotlib==3.8.4 jupyter
```

</div>

---

## 🧰 Stack Tecnológico

<div align="center">

[![My Skills](https://skillicons.dev/icons?i=python,opencv,git&theme=dark)](https://skillicons.dev)

</div>

<div align="center">

[![NumPy](https://img.shields.io/badge/NumPy-1.26.4-013243?style=flat-square&logo=numpy)]()
[![Matplotlib](https://img.shields.io/badge/Matplotlib-3.8.4-11557C?style=flat-square)]()
[![Jupyter](https://img.shields.io/badge/Jupyter%20Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)]()

</div>

---

## 🚀 Como Executar

```bash
# 1. Clone o repositório
git clone https://github.com/fassir/TratamentoCoresImagem.git
cd TratamentoCoresImagem

# 2. Crie ambiente virtual
python -m venv venv
source venv/bin/activate         # Linux/macOS
.\venv\Scripts\activate          # Windows

# 3. Instale as dependências
pip install opencv-python==4.8.0.76 numpy==1.26.4 matplotlib==3.8.4 jupyter

# 4. Adicione sua imagem de entrada
# Coloque um arquivo landscape.jpeg na raiz do projeto

# 5. Abra o notebook
jupyter notebook tratamento_de_imagem.ipynb

# 6. Execute todas as células sequencialmente
#    As saídas serão salvas automaticamente
```

---

## 🧪 Experimentos Sugeridos

- 🔄 **Outros espaços de cor:** Testar conversão HSV → cinza para comparar resultado visual
- 🎚️ **Thresholds adaptativos:** `cv2.adaptiveThreshold` para lidar com iluminação não uniforme
- 📐 **Filtros de suavização:** Aplicar blur Gaussiano antes do threshold para reduzir ruído
- 🎨 **Colorização falsa:** Mapear escala de cinza para um colormap (ex: `cv2.applyColorMap`)

---

## 📚 Referências

- [OpenCV Documentation](https://docs.opencv.org/4.8.0/)
- [NumPy Array Operations](https://numpy.org/doc/stable/reference/routines.array-manipulation.html)
- [Matplotlib Imshow](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.imshow.html)
- [PPM Format Specification](https://netpbm.sourceforge.net/doc/ppm.html)
- [Luma (ITU-R BT.601)](https://en.wikipedia.org/wiki/Luma_(video))

---

## 👤 Autor

<div align="center">

**Fabio Piassi**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/fabio-piassi)
[![GitHub](https://img.shields.io/badge/GitHub-171515?style=for-the-badge&logo=github&logoColor=white)](https://github.com/fassir)

</div>

---

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:16265F,50:2E75B6,100:1F9BD4&height=120&section=footer" width="100%"/>
