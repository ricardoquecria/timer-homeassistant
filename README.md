# Timer para Home Assistant com Node-RED

## Recursos usados
Button-card para exibir os botões com nomes personalizados
https://github.com/custom-cards/button-card



## Para criar esse timer vamos usar:
#### input_boolean
>* 1 input_boolean para iniciar a programação do timer
>* 1 input_boolean para cada dispositivo que você deseja acrescentar na lista do timer
#### input_number
#### input_select
#### sensor
#### Node-RED







```
input_boolean:
  #Boolean que será usado para iniciar o timer
  timer_ativar:
    name: Timer
    initial: off
    icon: mdi:progress-check


  #Booleans que serão usados para marcar quando um dispositivo está com timer agendado ou não
  timer_tv_quarto: #sugiro seguir essa formatação "timer
    name: Timer - TV do Quarto
    initial: off
    icon: mdi:television

  timer_lampadas:
    name: Timer - Lampadas
    initial: off
    icon: mdi:lightbulb-group-outline
```
