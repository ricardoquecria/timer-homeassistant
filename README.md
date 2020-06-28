# Timer para Home Assistant com Node-RED

## Recursos usados:
* Button-card para exibir os botões com nomes personalizados
https://github.com/custom-cards/button-card
Obs. O recurso button-card pode ser instalado através do HACS - Home Assistant Community Store

* Node-RED para criar a programação do timer
https://github.com/hassio-addons/addon-node-red
Obs. O Node-RED pode ser instalado através da Add-on Store dentro do Supervisor


## Entidades que usaremos:
#### input_boolean
#### input_number
#### input_select
#### sensor








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
