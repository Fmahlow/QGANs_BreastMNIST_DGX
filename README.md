# QGANs BreastMNIST

Este repositório reúne scripts para avaliar abordagens clássicas e quânticas de geração de imagens no BreastMNIST/MedMNIST. Ele contém entradas de linha de comando que executam experimentos completos (treino das GANs, métricas de qualidade das imagens sintéticas e testes com classificadores) gerando planilhas CSV e registros de configuração.

## Pré-requisitos

- Python 3.9 ou superior (os circuitos do PennyLane usam anotações de tipo do Python 3.9+).
- Dependências principais: `torch`, `torchvision`, `pennylane`, `pandas`, `scikit-learn`, `numpy`. Instale-as, por exemplo:

  ```bash
  pip install torch torchvision medmnist matplotlib torchmetrics seaborn scikit-learn scipy

  ```

- GPU é opcional, mas acelera os experimentos. Para usar o backend quântico `lightning.gpu`, instale `pennylane-lightning[gpu]` ou ajuste `--qml-backend` para um backend disponível.

## Estrutura dos scripts

### Arquivos para Rodar
- `run_classical_experiments.py`: Treina e avalia DCGAN, CGAN e WGAN-GP como baselines clássicos, exportando métricas em CSV.
- `run_patchqgan_experiments.py`: Executa apenas o pipeline do PatchQGAN para experimentos isolados.
- `run_mosaiq_experiments.py`: Executa apenas o pipeline MOSAIQ para experimentos isolados.

### Arquivos de Apoio
- `run_quantum_experiments.py`: Orquestra experimentos completos com os geradores quânticos PatchQGAN e MOSAIQ (inclusive classificadores e variações de balanceamento de dados sintéticos).
- `quantum_gan_medmnist.py`: Implementações das arquiteturas quânticas e utilitários compartilhados.
- `classical_gans.py`: Implementações das arquiteturas classicas e utilitários compartilhados.
- 
Todos os scripts salvam saídas em um diretório (padrão: `experiments_outputs`) e gravam um arquivo JSON com a configuração efetivamente utilizada.

## Como executar

### 1) Experimentos clássicos

O script aceita um JSON opcional com a configuração completa de `RunConfig`. Sem parâmetros, usa valores padrão (ex.: `breastmnist`, `gan_epochs=50`, `batch_size=128`, `repeats=5`).

```bash
python run_classical_experiments.py [opções]
```


### 2) Experimentos quânticos (PatchQGAN ou MOSAIQ)

```bash
python run_patchqgan_experiments.py [opções]
```

Principais flags (com padrões):

- `--data-flag`: identificador MedMNIST (padrão `breastmnist`).
- `--batch-size`: tamanho de batch para loaders clássicos (padrão `128`).
- `--gan-epochs` / `--clf-epochs`: épocas de treino das GANs e do classificador (padrões `50` e `3`).
- `--target-img-size`: tamanho final das imagens geradas (padrão `8`).
- `--latent-dim`: dimensão do ruído quântico (padrão `5`).
- `--n-a-qubits`: número de qubits ancilla (padrão `1`).
- `--q-depth`: profundidade do circuito quântico (padrão `6`).
- `--pca-dims`: dimensões da PCA usada para MOSAIQ (padrão `40`).
- `--mosaiq-batch-size`: batch específico do pipeline MOSAIQ (padrão `8`).
- `--repeats`: repetições de cada rodada (padrão `1`).
- `--output-dir`: pasta de resultados (padrão `experiments_outputs`).
- Backend PennyLane: `--qml-backend` (`lightning.qubit`), `--qml-diff-method` (`parameter-shift`), `--qml-batch-obs` (inteiro ou `None`), `--qml-mpi` (habilita MPI para `lightning.gpu`), `--qml-circuit-device` (força device Torch usado nas QNodes, ex.: `cuda`).

Exemplo prático:
```bash
python run_patchqgan_experiments.py --repeats=100
```


## Saídas e onde encontrar
Cada execução gera CSVs no diretório escolhido contendo métricas de treino, qualidade de síntese (FID/IS), estratégias de balanceamento e combinações de rácios de dados sintéticos. 
