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








```
input_boolean:

  #Input que será usado para ativar a sequencia da programação do timer. 
  timer_ativar:
    name: Timer
    initial: off
    icon: mdi:progress-check

  #Inputs que serão usados para marcar quando um dispositivo está com timer agendado ou não. 
  #Fazer um input_boolean para cada dispositivo que quiser adicionar na lista do timer.
  #Sugiro seguir essa formatação para facilitar a configuração "timer_nome_do_dispositivo"
  #Sugiro manter um padrão na formatação do nome, pois é esse nome que será exibido quando o timer estiver ativo.
  timer_tv_quarto: 
    name: Timer - TV do Quarto 
    initial: off
    icon: mdi:television

  timer_lampadas:
    name: Timer - Lampadas
    initial: off
    icon: mdi:lightbulb-group-outline
```
