# Timer para Home Assistant com Node-RED

## Recursos usados:
* Button-card para exibir os botões com nomes personalizados https://github.com/custom-cards/button-card
<br>Obs. O recurso button-card pode ser instalado através do HACS - Home Assistant Community Store

* Node-RED para criar a programação do timer https://github.com/hassio-addons/addon-node-red
<br>Obs. O Node-RED pode ser instalado através da Add-on Store dentro do Supervisor


## Entidades que usaremos:
* input_boolean
* input_number
* input_select
* sensor

## Input Boolean:

Começamos com o input que será usado para ativar a sequencia da programação do timer; 
```
input_boolean:

  #Input para ativar o timer 
  timer_ativar:
    name: Timer
    initial: off
    icon: mdi:progress-check
```

Depois acrescentamos os inputs para cada dispositivo. <br>
Esses inputs serão usados como botões que sinalizam quando um timer estiver ativado e para cancelar um timer ao clicar.<br>
Faça um input_boolean para cada dispositivo que desejar adicionar na lista do timer.
```
  #Mantenha essa formatação para facilitar a configuração "timer_nome_do_dispositivo"
  #Mantenha um padrão na formatação do nome, pois é esse nome que será exibido quando o timer estiver ativo.
  
  timer_tv_quarto:
    name: Timer - TV do Quarto 
    initial: off
    icon: mdi:television

  timer_lampadas:
    name: Timer - Lampadas
    initial: off
    icon: mdi:lightbulb-group-outline
```

## Input Number:
Vamos criar um input_number que será usado para definir os minutos do timer.<br>
Você pode escolher entre um input com slider (como no gif).
```
input_number:

  timer:
    icon: mdi:timer
    name: Temporizador
    initial: 5
    min: 5
    max: 180
    step: 5
```
Ou pode escolher um input com box para digitar o valor:
```
input_number:

  timer:
    icon: mdi:timer
    name: Temporizador
    initial: 5
    min: 5
    max: 180
    step: 5
    mode: box
```
