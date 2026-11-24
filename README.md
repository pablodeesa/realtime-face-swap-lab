# 🎭 Real-Time Computer Vision & Virtual Camera Pipeline (PoC)

Laboratório prático de processamento de imagem em tempo real utilizando redes neurais para troca facial (*face swap*) com roteamento de vídeo via câmera virtual no Windows.

O objetivo técnico deste laboratório é explorar a arquitetura de inferência local (`ONNX Runtime`), aceleração de hardware, roteamento de dispositivos virtuais e demonstrar na prática a facilidade atual de criação de *deepfakes* em tempo real — reforçando a importância de protocolos de verificação e defesa contra engenharia social em comunicações corporativas.

---

## 🏗️ Arquitetura do Pipeline

```text
[Webcam Física (USB)] 
       │
       ▼
[Deep-Live-Cam (Python / InsightFace / ONNX)] ──► Inferência em Tempo Real
       │
       ▼ (Janela Live Preview)
[OBS Studio (Window Capture + Crop)] ──► OBS Virtual Camera Driver
       │
       ▼
[Aplicações de Videoconferência (WhatsApp Desktop / Teams / Meet)]

🛠️ Stack & Ferramentas Utilizadas
Ambiente & Automação: Windows PowerShell, winget, Python venv

Motor de Visão Computacional: Deep-Live-Cam (Open Source)

Modelos de Inferência: inswapper_128.onnx & pacote buffalo_l (detecção facial 2D/3D det_10g / 1k3d68)

Runtime & Aceleração: ONNX Runtime (CPUExecutionProvider / DirectML / CUDA)

Roteamento de Vídeo: OBS Studio (Captura de Janela + Recorte de Região + Câmera Virtual)

🚀 Passo a Passo de Implantação (PowerShell)
1. Pré-requisitos do Sistema
Instalação automatizada via gerenciador de pacotes nativo do Windows:

PowerShell
winget install --id Git.Git -e --source winget
winget install --id Python.Python.3.10 -e --source winget
winget install --id Gyan.FFmpeg -e --source winget
winget install --id OBSProject.OBSStudio -e --source winget
2. Clonagem e Isolamento de Ambiente
PowerShell
mkdir C:\IA -Force
cd C:\IA
git clone [https://github.com/hacksider/Deep-Live-Cam.git](https://github.com/hacksider/Deep-Live-Cam.git)
cd Deep-Live-Cam

python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
3. Execução
PowerShell
python run.py
🔧 Troubleshooting & Lições Aprendidas no Lab
Durante a implementação, foram mapeados e resolvidos os seguintes gargalos técnicos:

Fallback de GPU para CPU (cublasLt64_12.dll missing):

Sintoma: O onnxruntime-gpu falhou ao carregar o CUDAExecutionProvider por ausência das bibliotecas nativas do CUDA 12 / cuDNN 9 no PATH, realizando fallback automático para CPUExecutionProvider.

Solução Alternativa: Utilização de onnxruntime-directml para aceleração de GPU nativa via DirectX 12 no Windows sem necessidade de instalar o pacote completo do CUDA Toolkit.

Concorrência de Hardware (Device Lock):

Sintoma: Aplicações de chamada exibem erro de dispositivo em uso ("Your camera is being used by another app").

Causa: O Windows bloqueia o acesso simultâneo de dois processos à mesma webcam USB física.

Solução: Desacoplamento via OBS Virtual Camera, capturando exclusivamente a janela de saída (Live Preview) com recorte de bordas (Alt + Drag) e entregando um stream virtual limpo para o cliente de vídeo.

Sensibilidade à Iluminação (Contra-luz):

Observação: Ambientes com alta luminosidade de fundo (backlight) reduzem a confiança do detector det_10g.onnx, exigindo iluminação frontal uniforme para manter a estabilidade do rastreamento facial.

⚠️ Conformidade Legal, Ética e Privacidade (LGPD)
Este projeto foi desenvolvido exclusivamente para fins de estudo, pesquisa em visão computacional e conscientização em segurança da informação (Blue Team / Cyber Awareness).

Durante os testes de validação de rastreamento facial em ambiente local fechado, foram utilizadas imagens públicas de referência apenas para aferição técnica do algoritmo. Em estrito cumprimento à Lei Geral de Proteção de Dados (LGPD - Lei nº 13.709/2018) e às normas brasileiras de Proteção ao Direito de Imagem, nenhum registro visual contendo rostos de terceiros ou dados sensíveis é armazenado, distribuído ou exibido neste repositório — todas as evidências visuais foram sanitizadas com avatares sintéticos.
