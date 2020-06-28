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

## Configuração


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
Resultado:
* input_boolean.timer_ativar
* input_boolean.timer_tv_quarto
* input_boolean.timer_lampadas



## Input Number:
Vamos criar um input_number que será usado para definir os minutos do timer.<br>
Você pode escolher um input com slider (como no gif).
```
input_number:

  timer_minutos:
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

  timer_minutos:
    icon: mdi:timer
    name: Temporizador
    initial: 5
    min: 5
    max: 180
    step: 5
    mode: box
```
Resultado:
* input_number.timer_minutos


## Input Select:

O input_select será usado para criar a lista de seleção com os dispositivos na interface do timer.<br>
Acrescente as oções de acordo com sua necessidade.<br>
ATENÇÃO! Só acrescente opcões que tiverem input_booleans criados.
```
input_select:

  timer_lista:
    name: 'Timer - Dispositivos'
    icon: mdi:devices
    options: #Só acrescente as opções que tiverem input_boolean
      - TV do Quarto
      - Lampadas
```
Resultado:
* input_select.timer_lista

## Sensor

Vamos criar sensores com templates para calcular e exibir o tempo que ainda falta para o dispositivo ser desligado.<br>
ATENÇÃO! Se ao criar mais sensores para os seus dispositivos, altere também o template do calculo. Por isso que é importante manter um padrão "timer_nome_do_dispositivo". Observe que o "value_template" faz referência ao próprio sensor e ao input_boolean do dispositivo em questão. Compare os dois exemplos abaixo.
```
sensor:
  - platform: template
    sensors:

      timer_tv_quarto:
        friendly_name: 'Timer - TV do Quarto'
        unit_of_measurement: min
        value_template: "{% if (states.sensor.time.state) | float == 0 %}
        {{ ((((state_attr('sensor.timer_tv_quarto', 'timer') | float()) * 60) - (as_timestamp(now()) -      as_timestamp(states.input_boolean.timer_tv_quarto.last_changed))) / 60) | round(0) }}
        {% endif %}"
        attribute_templates:
          timer: >-
            {{ state_attr('sensor.timer_tv_quarto', 'timer')}}

      timer_lampadas:
        friendly_name: 'Timer - Lampadas'
        unit_of_measurement: min
        value_template: "{% if (states.sensor.time.state) | float == 0 %}
        {{ ((((state_attr('sensor.timer_lampadas', 'timer') | float()) * 60) - (as_timestamp(now()) - as_timestamp(states.input_boolean.timer_lampadas.last_changed))) / 60) | round(0) }}
        {% endif %}"
        attribute_templates:
          timer: >-
            {{ state_attr('sensor.timer_lampadas', 'timer')}}
```
Resultado:
* sensor.timer_tv_quarto
* sensor.timer_lampadas


## Node-RED
```
[
    {
        "id": "5e026a39.f4ae64",
        "type": "server-state-changed",
        "z": "d342a46b.72a638",
        "name": "Start Timer",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "input_boolean.timer_start",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "on",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 2,
        "output_only_on_state_change": true,
        "x": 80,
        "y": 400,
        "wires": [
            [
                "d3fcb50.fccae48",
                "be399d5c.b76da"
            ],
            []
        ]
    },
    {
        "id": "d3fcb50.fccae48",
        "type": "api-current-state",
        "z": "d342a46b.72a638",
        "name": "Lampadas",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "outputs": 2,
        "halt_if": "Lampadas",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "override_topic": false,
        "entity_id": "input_select.timer_lista",
        "state_type": "str",
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "blockInputOverrides": false,
        "x": 250,
        "y": 380,
        "wires": [
            [
                "34f3563d.c8da7a"
            ],
            []
        ]
    },
    {
        "id": "34f3563d.c8da7a",
        "type": "api-render-template",
        "z": "d342a46b.72a638",
        "name": "Set Delay",
        "server": "8c51b0bb.698c3",
        "template": "{{( (states('input_number.timer_minutos') | float()) * 60000 ) | round()}}",
        "resultsLocation": "delay",
        "resultsLocationType": "msg",
        "templateLocation": "delay",
        "templateLocationType": "msg",
        "x": 420,
        "y": 380,
        "wires": [
            [
                "d962b823.4b7298"
            ]
        ]
    },
    {
        "id": "b33aa916.391678",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "Reset Botão Ativar",
        "server": "82136dea.a6d7a",
        "version": 1,
        "debugenabled": false,
        "service_domain": "input_boolean",
        "service": "turn_off",
        "entityId": "input_boolean.timer_ativar",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1210,
        "y": 380,
        "wires": [
            [
                "84bf864c.aa2258"
            ]
        ]
    },
    {
        "id": "7bca1ff8.8b7c1",
        "type": "ha-api",
        "z": "d342a46b.72a638",
        "name": "Set Sensor",
        "server": "82136dea.a6d7a",
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "/api/states/sensor.timer_lampadas",
        "data": "{\"entityid\":\"sensor.timer_lampadas\",\"state\":0,\"attributes\":{\"timer\":\"{{payload}}\"}}",
        "dataType": "json",
        "location": "payload",
        "locationType": "msg",
        "responseType": "json",
        "x": 730,
        "y": 380,
        "wires": [
            [
                "14a04f31.18e7c1"
            ]
        ]
    },
    {
        "id": "d962b823.4b7298",
        "type": "api-render-template",
        "z": "d342a46b.72a638",
        "name": "get var Timer",
        "server": "8c51b0bb.698c3",
        "template": "{{ states('input_number.timer_minutos') }}",
        "resultsLocation": "payload",
        "resultsLocationType": "msg",
        "templateLocation": "payload",
        "templateLocationType": "msg",
        "x": 570,
        "y": 380,
        "wires": [
            [
                "7bca1ff8.8b7c1"
            ]
        ]
    },
    {
        "id": "84bf864c.aa2258",
        "type": "delay",
        "z": "d342a46b.72a638",
        "name": "",
        "pauseType": "delayv",
        "timeout": "5",
        "timeoutUnits": "seconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": true,
        "x": 1380,
        "y": 380,
        "wires": [
            [
                "e625c572.9c1d08"
            ]
        ]
    },
    {
        "id": "5244d21f.e1409c",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "turn_off Boolean",
        "server": "82136dea.a6d7a",
        "version": 1,
        "debugenabled": false,
        "service_domain": "input_boolean",
        "service": "turn_off",
        "entityId": "input_boolean.timer_lampadas",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1850,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "d2019b8.a92cf68",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "turn_off Device",
        "server": "82136dea.a6d7a",
        "version": 1,
        "debugenabled": false,
        "service_domain": "homeassistant",
        "service": "turn_off",
        "entityId": "group.all_lights",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1660,
        "y": 380,
        "wires": [
            [
                "5244d21f.e1409c"
            ]
        ]
    },
    {
        "id": "e625c572.9c1d08",
        "type": "api-current-state",
        "z": "d342a46b.72a638",
        "name": "Check",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "outputs": 2,
        "halt_if": "on",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "override_topic": false,
        "entity_id": "input_boolean.timer_lampadas",
        "state_type": "str",
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "blockInputOverrides": true,
        "x": 1510,
        "y": 380,
        "wires": [
            [
                "d2019b8.a92cf68"
            ],
            []
        ]
    },
    {
        "id": "3d6d49ad.945456",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "turn_on Boolean",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "debugenabled": false,
        "service_domain": "input_boolean",
        "service": "turn_on",
        "entityId": "input_boolean.timer_lampadas",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1020,
        "y": 380,
        "wires": [
            [
                "b33aa916.391678"
            ]
        ]
    },
    {
        "id": "14a04f31.18e7c1",
        "type": "delay",
        "z": "d342a46b.72a638",
        "name": "500ms",
        "pauseType": "delay",
        "timeout": "500",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": true,
        "x": 870,
        "y": 380,
        "wires": [
            [
                "3d6d49ad.945456"
            ]
        ]
    },
    {
        "id": "4ad42e2c.f3091",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 1",
        "info": "### Aqui o Node-RED ativa o fluxo se o input_boolean.timer_start for ativado.",
        "x": 70,
        "y": 360,
        "wires": []
    },
    {
        "id": "d3f88d76.9eb97",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 2",
        "info": "### Aqui verificamos se o nome do disposito que foi selecionado no input_select.timer_lista é \"Lampadas\" \n\n### É preciso alterar o \"If State is\" e o \"Name\" para o mesmo nome que foi acrescentado no input_select. \n### Nesse ponto é muito importante manter a mesma escrita, respeitando letras maiúsculas e minúsculas que foram usadas na hora de criar o input_select.",
        "x": 230,
        "y": 340,
        "wires": []
    },
    {
        "id": "d90eecae.dd32c",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 3",
        "info": "### Código que vai coletar e converter o tempo que foi escolhido no input_number.timer para programar o delay automaticamente.",
        "x": 410,
        "y": 340,
        "wires": []
    },
    {
        "id": "a48af692.306b08",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 4",
        "info": "# NÃO ALTERAR\n### Coleta o valor do input_number.timer só para configurar o sensor. ",
        "x": 550,
        "y": 340,
        "wires": []
    },
    {
        "id": "13e1ff75.8c6d81",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 5",
        "info": "### Altere o nome do sensor no campo \"Path\" e no campo \"Data\".",
        "x": 710,
        "y": 340,
        "wires": []
    },
    {
        "id": "a18285ee.50be98",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 6",
        "info": "### Aqui o input_boolean é definido como \"on\" para sinalizar que o timer está ativo. \n## Alterar o campo \"Entity Id\"",
        "x": 990,
        "y": 340,
        "wires": []
    },
    {
        "id": "11d7086f.b38a38",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 6",
        "info": "### Aqui o botão input_boolean.timer_ativar volta ao seu estado original para estar pronto para ser ativado novamente.",
        "x": 1170,
        "y": 340,
        "wires": []
    },
    {
        "id": "18344dbd.ee3262",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 7",
        "info": "### Aqui o delay vai coletar automaticamente o tempo do delay definido no \"Set Delay\" - Leia 3",
        "x": 1370,
        "y": 340,
        "wires": []
    },
    {
        "id": "18859cdb.908993",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 8",
        "info": "# Altere o input_boolean no \"Entity Id\"\n\n### Nesse ponto o Node-RED vai checar se esse timer permanece ativo antes de efetuar o desligamente. Se vc ativar um timer e depois cancelar, então quando chegar nesse ponto o Node-RED vai checar e desarmar o desligamento.\n\n",
        "x": 1510,
        "y": 340,
        "wires": []
    },
    {
        "id": "3695b541.6e704a",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 9",
        "info": "# Altere o \"Entity Id\" e coloque a entidade que deve ser desligada ao final desse fluxo.\n\n### Exemplo: group.lampadas ou switch.ar_condicionado\n\n",
        "x": 1630,
        "y": 340,
        "wires": []
    },
    {
        "id": "a3c6c088.765a5",
        "type": "comment",
        "z": "d342a46b.72a638",
        "name": "Leia 10",
        "info": "# Altere o input_boolean no \"Entity Id\"\n\n### Aqui o botão input_boolean do dispositivo é definido como off para sinalziar que o timer não está mais ativo.\n\n",
        "x": 1810,
        "y": 340,
        "wires": []
    },
    {
        "id": "be399d5c.b76da",
        "type": "api-current-state",
        "z": "d342a46b.72a638",
        "name": "TV do Quarto",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "outputs": 2,
        "halt_if": "TV do Quarto",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "override_topic": false,
        "entity_id": "input_select.timer_lista",
        "state_type": "str",
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "blockInputOverrides": false,
        "x": 260,
        "y": 420,
        "wires": [
            [
                "4828ed42.910a94"
            ],
            []
        ]
    },
    {
        "id": "4828ed42.910a94",
        "type": "api-render-template",
        "z": "d342a46b.72a638",
        "name": "Set Delay",
        "server": "8c51b0bb.698c3",
        "template": "{{( (states('input_number.timer_minutos') | float()) * 60000 ) | round()}}",
        "resultsLocation": "delay",
        "resultsLocationType": "msg",
        "templateLocation": "delay",
        "templateLocationType": "msg",
        "x": 420,
        "y": 420,
        "wires": [
            [
                "3c6fb418.840cbc"
            ]
        ]
    },
    {
        "id": "746f9d05.88bc64",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "Reset Botão Ativar",
        "server": "82136dea.a6d7a",
        "version": 1,
        "debugenabled": false,
        "service_domain": "input_boolean",
        "service": "turn_off",
        "entityId": "input_boolean.timer_ativar",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1210,
        "y": 420,
        "wires": [
            [
                "fbc42508.9b8028"
            ]
        ]
    },
    {
        "id": "43e00d00.0ae4f4",
        "type": "ha-api",
        "z": "d342a46b.72a638",
        "name": "Set Sensor",
        "server": "82136dea.a6d7a",
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "/api/states/sensor.timer_tv_quarto",
        "data": "{\"entityid\":\"sensor.timer_tv_quarto\",\"state\":0,\"attributes\":{\"timer\":\"{{payload}}\"}}",
        "dataType": "json",
        "location": "payload",
        "locationType": "msg",
        "responseType": "json",
        "x": 730,
        "y": 420,
        "wires": [
            [
                "c0a6d1ca.a77b7"
            ]
        ]
    },
    {
        "id": "3c6fb418.840cbc",
        "type": "api-render-template",
        "z": "d342a46b.72a638",
        "name": "get var Timer",
        "server": "8c51b0bb.698c3",
        "template": "{{ states('input_number.timer_minutos') }}",
        "resultsLocation": "payload",
        "resultsLocationType": "msg",
        "templateLocation": "payload",
        "templateLocationType": "msg",
        "x": 570,
        "y": 420,
        "wires": [
            [
                "43e00d00.0ae4f4"
            ]
        ]
    },
    {
        "id": "fbc42508.9b8028",
        "type": "delay",
        "z": "d342a46b.72a638",
        "name": "",
        "pauseType": "delayv",
        "timeout": "5",
        "timeoutUnits": "seconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": true,
        "x": 1380,
        "y": 420,
        "wires": [
            [
                "5712be6c.130ff"
            ]
        ]
    },
    {
        "id": "6f23d751.a12768",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "turn_off Boolean",
        "server": "82136dea.a6d7a",
        "version": 1,
        "debugenabled": false,
        "service_domain": "input_boolean",
        "service": "turn_off",
        "entityId": "input_boolean.timer_tv_quarto",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1850,
        "y": 420,
        "wires": [
            []
        ]
    },
    {
        "id": "d844933.ba5db7",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "turn_off Device",
        "server": "82136dea.a6d7a",
        "version": 1,
        "debugenabled": false,
        "service_domain": "homeassistant",
        "service": "turn_off",
        "entityId": "switch.tv_quarto",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1660,
        "y": 420,
        "wires": [
            [
                "6f23d751.a12768"
            ]
        ]
    },
    {
        "id": "5712be6c.130ff",
        "type": "api-current-state",
        "z": "d342a46b.72a638",
        "name": "Check",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "outputs": 2,
        "halt_if": "on",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "override_topic": false,
        "entity_id": "input_boolean.timer_tv_quarto",
        "state_type": "str",
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "blockInputOverrides": true,
        "x": 1510,
        "y": 420,
        "wires": [
            [
                "d844933.ba5db7"
            ],
            []
        ]
    },
    {
        "id": "72806c50.baec34",
        "type": "api-call-service",
        "z": "d342a46b.72a638",
        "name": "turn_on Boolean",
        "server": "8c51b0bb.698c3",
        "version": 1,
        "debugenabled": false,
        "service_domain": "input_boolean",
        "service": "turn_on",
        "entityId": "input_boolean.timer_tv_quarto",
        "data": "",
        "dataType": "json",
        "mergecontext": "",
        "output_location": "",
        "output_location_type": "none",
        "mustacheAltTags": false,
        "x": 1020,
        "y": 420,
        "wires": [
            [
                "746f9d05.88bc64"
            ]
        ]
    },
    {
        "id": "c0a6d1ca.a77b7",
        "type": "delay",
        "z": "d342a46b.72a638",
        "name": "500ms",
        "pauseType": "delay",
        "timeout": "500",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": true,
        "x": 870,
        "y": 420,
        "wires": [
            [
                "72806c50.baec34"
            ]
        ]
    },
    {
        "id": "8c51b0bb.698c3",
        "type": "server",
        "z": "",
        "name": "Home Assistant",
        "addon": true
    },
    {
        "id": "82136dea.a6d7a",
        "type": "server",
        "z": "",
        "name": "Home Assistant",
        "legacy": false,
        "addon": true,
        "rejectUnauthorizedCerts": true,
        "ha_boolean": "y|yes|true|on|home|open",
        "connectionDelay": true,
        "cacheJson": true
    }
]
```

