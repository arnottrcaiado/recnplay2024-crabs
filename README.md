### Projeto Completo e Detalhado: Caranguejo Interativo com Sensores, Emoções e Animações Dinâmicas

Este projeto consiste na criação de um caranguejo interativo, **Chico**, que responde às interações do público captadas por sensores. Ele simula emoções e comportamentos dinâmicos, reagindo a estímulos como proximidade, toque, concentração de pessoas e posição no ambiente. Chico é projetado para parecer "vivo", com expressões faciais animadas em pixel art exibidas em uma tela de 50 polegadas, e seu corpo é construído a partir de materiais reciclados, como teclados, monitores antigos e chips descartados.

### História do Caranguejo

**Chico** é um caranguejo cibernético que simboliza a sustentabilidade tecnológica, sendo construído com materiais reciclados de eletrônicos. Ele vive em um espaço interativo, reagindo às pessoas que passam por ele. Seu objetivo é não apenas entreter, mas também educar sobre o reuso de materiais e a importância de um futuro tecnológico sustentável.

Chico expressa emoções como felicidade, tristeza, surpresa e cansaço, dependendo das interações que capta dos sensores. Ele também tem uma funcionalidade especial, o **Momento Instagramável**, onde adota uma pose amigável e convida os visitantes a tirarem uma foto.

### Interações Sensoriais

Chico é equipado com sensores que captam diferentes tipos de interações:

- **Sensores de Câmera**: Detectam a concentração de pessoas e seus movimentos, fazendo com que Chico acompanhe visualmente as pessoas e ajuste suas emoções.
- **Sensores de Proximidade**: Capturam a distância entre o público e o caranguejo, modificando suas reações com base na proximidade.
- **Sensores de Toque**: Quando alguém toca o corpo físico de Chico, ele reage com expressões de alegria ou surpresa.
- **Sensores de Posição e Distância**: Captam o movimento das pessoas ao redor, permitindo que Chico acompanhe os visitantes com seus olhos e mude suas emoções.

### Funcionalidade Especial: **Momento Instagramável**

Quando os sensores detectam que uma pessoa está na posição ideal para tirar uma foto, Chico ativa o **Momento Instagramável**, exibindo uma expressão alegre e uma mensagem visual que convida os visitantes a tirarem uma foto. Essa função proporciona uma interação única e cria um momento divertido para o público.

### Estrutura do Projeto

```bash
/root
    ├── Crab.gd                # Classe principal
    ├── SensorManager.gd        # Gerenciamento de sensores
    ├── EmotionManager.gd       # Gerenciamento das emoções, incluindo "momento instagramável"
    ├── AnimationManager.gd     # Controle de animações e transições entre estados emocionais
    ├── UIManager.gd            # Interface do usuário
    └── assets/                 # Imagens dos estados emocionais em pixel art
        ├── happy.png
        ├── sad.png
        ├── surprised.png
        ├── sleepy.png
        ├── neutral.png
        ├── angry.png
        ├── hungry.png
        ├── full.png
        ├── scared.png
        ├── laughing.png
        ├── thinking.png
        ├── curious.png
        ├── tired.png
        ├── alert.png
        ├── confused.png
        ├── bored.png
        ├── excited.png
        ├── anxious.png
        ├── satisfied.png
        ├── disappointed.png
        ├── shocked.png
        ├── instagram_ready.png  # Nova imagem para "momento instagramável"
```

### Código Completo

#### **Crab.gd** (Classe principal)

```gdscript
extends Node2D

# Referências para os módulos principais
var sensor_manager
var emotion_manager
var animation_manager
var ui_manager

func _ready():
    sensor_manager = $SensorManager
    emotion_manager = $EmotionManager
    animation_manager = $AnimationManager
    ui_manager = $UIManager

    # Conecta os sensores aos módulos
    sensor_manager.connect("sensor_data_received", self, "_on_sensor_data_received")
    emotion_manager.initialize()
    animation_manager.initialize()

# Função chamada quando dados dos sensores são recebidos
func _on_sensor_data_received(sensor_data):
    # Processa emoções com base nos dados dos sensores
    emotion_manager.process_sensor_data(sensor_data)
    # Atualiza animações conforme a emoção atual
    animation_manager.update_emotion(emotion_manager.current_emotion)

# Atualiza a interface de acordo com a emoção atual
func _process(delta):
    ui_manager.update_ui(emotion_manager.current_emotion)
```

#### **SensorManager.gd** (Gerenciamento dos Sensores)

```gdscript
extends Node

signal sensor_data_received(sensor_data)

# Função para capturar dados dos sensores
func _ready():
    set_process(true)

# Processamento periódico dos sensores
func _process(delta):
    if OS.get_ticks_msec() % 2000 == 0:
        var sensor_data = {
            "camera": detect_people_concentration(),
            "touch": randi() % 2,                    # 0 ou 1 para toque
            "proximity": randi() % 100,              # Proximidade
            "position": Vector2(randf(), randf()),   # Posição simulada
            "instagram_position": check_instagram_position() # Detecta posição para foto
        }
        emit_signal("sensor_data_received", sensor_data)

# Detecta a concentração de pessoas no ambiente
func detect_people_concentration():
    return randi() % 50  # Simula a detecção de concentração de pessoas

# Verifica se os visitantes estão na posição ideal para tirar fotos
func check_instagram_position():
    var position = Vector2(randf(), randf())
    return position.distance_to(Vector2(0.5, 0.5)) < 0.1  # Posição ideal para o momento Instagramável
```

#### **EmotionManager.gd** (Gerenciamento das Emoções)

```gdscript
extends Node

var current_emotion = "neutral"  # Emoção inicial do caranguejo

# Tabela de emoções e condições para ativação
var emotions = {
    "happy": {"touch": 10, "proximity": 0, "camera": 10},
    "sad": {"touch": 0, "proximity": 0, "camera": 5},
    "surprised": {"touch": 5, "proximity": 80, "camera": 30},
    "sleepy": {"touch": 0, "proximity": 0, "camera": 0},
    "instagram_ready": {"instagram_position": true},  # Estado especial para fotos
}

# Inicializa o estado emocional
func initialize():
    current_emotion = "neutral"

# Processa os dados dos sensores e define a emoção atual
func process_sensor_data(sensor_data):
    var touch_score = sensor_data["touch"]
    var proximity_score = sensor_data["proximity"]
    var people_concentration = sensor_data["camera"]
    var instagram_position = sensor_data["instagram_position"]
    
    # Lógica para mudar as emoções com base nos dados dos sensores
    if instagram_position:
        current_emotion = "instagram_ready"
    elif touch_score > 8 and proximity_score < 20:
        current_emotion = "happy"
    elif proximity_score > 70:
        current_emotion = "surprised"
    elif people_concentration < 5:
        current_emotion = "sad"
    else:
        current_emotion = "neutral"
```

#### **AnimationManager.gd** (Controle das Animações)

```gdscript
extends Node

var animation_player

# Inicializa o AnimationPlayer
func initialize():
    animation_player = $AnimationPlayer

# Atualiza a animação de acordo com a emoção atual
func update_emotion(emotion):
    match emotion:
        "happy":
            animation_player.play("happy_transition")
        "sad":
            animation_player.play("sad_transition")
        "surprised":
            animation_player.play("surprised_transition")
        "sleepy":
            animation_player.play("sleepy_transition")
        "instagram_ready":
            animation_player.play("instagram_ready_transition")  # Animação especial para o momento da foto
        _:
            animation_player.play("neutral_transition")
```

#### **UIManager.gd** (Interface do Usuário)

```gdscript
extends Node2D

var emotion_label

# Inicializa a UI
func _ready():
    emotion_label = $EmotionLabel

# Atualiza o texto da interface de acordo com a emoção atual
func update_ui(emotion):
    if emotion == "instagram_ready":
        emotion_label.text = "Pronto para a foto! #MomentoInstagramável"
    else:
        emotion_label.text = "Current Emotion: " + emotion
```

### Explicação Detalhada das Emoções e Transições

- **Happy**: Ativada quando Chico é tocado ou as pessoas estão próximas.
- **Sad**: Ativada quando há pouca interação com o público (pouca concentração de pessoas).
- **Surprised**: Ocorre quando a proximidade das pessoas é alta ou há um grande grupo no ambiente.
- **Sleepy**: Chico entra nesse estado quando não há interação por um tempo prolongado.
- **Instagram Ready**: O estado especial para fotos, ativado quando uma pessoa está na posição ideal para tirar uma foto com Chico.

---

###

 Prompts Utilizados para Gerar o Projeto

1. **Prompt Inicial para Estrutura Geral:**
   - *"Esboce uma lógica eficiente para uma aplicação interativa e gamificada de um caranguejo, com reações baseadas em sensores de toque, proximidade, posição, energia e horas do dia. O código será feito em Godot, utilizando sensores e um display para expressões faciais."*

2. **Prompt para Expansão da Estrutura e Lógica dos Sensores:**
   - *"Aprofunde o código no Godot com classes, funções e métodos para controlar as emoções e reações do caranguejo com base nos dados recebidos dos sensores. O caranguejo deve reagir aos estímulos dos sensores de toque, proximidade, posição e contar com um comportamento especial de 'Momento Instagramável', onde ele assume uma pose ideal para fotos."*

3. **Prompt para Detalhamento das Emoções e Animações:**
   - *"Agora detalhe a estrutura do código, adicionando transições entre estados emocionais e controlando as animações de acordo com as emoções do caranguejo. As emoções incluem: feliz, triste, surpreso, cansado e o estado especial 'Instagram Ready'."*

4. **Prompt para Adicionar Integração Completa com os Sensores:**
   - *"Adicione a captura de dados dos sensores no código, incluindo a lógica para detectar a proximidade, o toque, a concentração de pessoas por câmeras e a posição dos visitantes. Esses dados serão usados para definir o estado emocional do caranguejo."*

5. **Prompt para Função Especial 'Momento Instagramável':**
   - *"Crie uma função especial chamada 'Momento Instagramável', onde o caranguejo assume uma pose alegre e convidativa para fotos quando alguém se posiciona em um local específico captado pelos sensores de câmera."*

6. **Prompt para Animações e Transições Detalhadas:**
   - *"Detalhe as animações de transição entre os estados emocionais do caranguejo, como piscadas, mudanças de expressão e ajustes suaves entre as emoções."*

7. **Prompt para Interface de Usuário (UI):**
   - *"Adicione uma interface de usuário (UI) que exiba a emoção atual do caranguejo, utilizando textos que mudam de acordo com a emoção ativa. A interface também deve exibir uma mensagem especial quando o 'Momento Instagramável' estiver ativo."*

---
