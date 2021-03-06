---
title: Button
weight: 312
description: Descrição do componentes Button e seus atributos
---

---

## O que é?

Este componente irá definir as propriedades de um botão nativo através do Beagle. 

A sua estrutura está representada abaixo: 

<table>
  <thead>
    <tr>
      <th style="text-align:left"><strong>Atributos</strong>
      </th>
      <th style="text-align:left"><strong>Tipo</strong>
      </th>
      <th style="text-align:center">Obrigat&#xF3;rio</th>
      <th style="text-align:left"><strong>Defini&#xE7;&#xE3;o</strong>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">text</td>
      <td style="text-align:left">
        String ou
        <a href="../../contexto/#bindings">Binding</a>
      </td>
      <td style="text-align:center">&#x2713;</td>
      <td style="text-align:left">Texto no bot&#xE3;o. T&#xED;tulo do bot&#xE3;o</td>
    </tr>
    <tr>
      <td style="text-align:left">styleId</td>
      <td style="text-align:left">
        String ou
        <a href="../../contexto/#bindings">Binding</a>
      </td>
      <td style="text-align:center"></td>
      <td style="text-align:left">Este atributo referencia um estilo nativo a ser aplicado no bot&#xE3;o.
        Se n&#xE3;o for informado, o estilo padr&#xE3;o de bot&#xE3;o da sua aplica&#xE7;&#xE3;o
        ser&#xE1; aplicado.</td>
    </tr>
    <tr>
      <td style="text-align:left">onPress</td>
      <td style="text-align:left">List &lt;<a href="../../acoes/">Action</a>&gt;</td>
      <td style="text-align:center"></td>
      <td style="text-align:left">Array de a&#xE7;&#xF5;es que o bot&#xE3;o dispara quando clicado.
        &#xC9; poss&#xED;vel definir uma A&#xE7;&#xE3;o customizada ou qualquer
        a&#xE7;&#xE3;o j&#xE1; dispon&#xED;vel na interface, como por exemplo uma
        a&#xE7;&#xE3;o que mostra uma mensagem de alerta(<a href="../../acoes/alert">Alert</a>).
        Este atributo &#xE9; opcional, mas se uma a&#xE7;&#xE3;o for definida aqui
        ela precisa estar configurada no frontend. Para criar uma a&#xE7;&#xE3;o
        no frontend veja o exemplo: <a href="../../../features/criacao-de-novas-acoes">Criando uma a&#xE7;&#xE3;o customizada</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">enabled</td>
      <td style="text-align:left">Bind&lt;Boolean&gt;</td>
      <td style="text-align:center"></td>
      <td style="text-align:left">Propriedade para habilitar ou desabilitar.</td>
    </tr>
    <tr>
      <td style="text-align:left">clickAnalyticsEvent</td>
      <td style="text-align:left"><a href="../../analytics">ClickEvent</a>
      </td>
      <td style="text-align:center"></td>
      <td style="text-align:left">Evento de clique que ser&#xE1; disparado caso tenha sido implementado
        um servi&#xE7;o de analytics. Para saber mais sobre analytics e os eventos
        suportados, veja o exemplo: <a href="../../analytics">Analytics</a>.</td>
    </tr>
  </tbody>
</table>

## Como usar?

{{< tabs id="T133" >}}
{{% tab name="JSON" %}}
<!-- json-playground:button.json
{
  "_beagleComponent_": "beagle:button",
  "text": "Clique aqui",
  "styleId" : "DesignSystem.MyNativeStyle",
  "onPress": [
    {
      "_beagleAction_": "beagle:alert",
      "message": "Exemplo de botão"
    }
  ]
}
-->
{{% playground file="button.json" language="pt" %}}
{{% /tab %}}

{{% tab name="Kotlin DSL" %}}
```kotlin
Button(
    text = "Clique aqui",
    styleId = "DesignSystem.MyNativeStyle",
    onPress = listOf(Alert(message="Exemplo de botão"))
)
```
{{% /tab %}}
{{< /tabs >}}

### 👉 [Teste esse componente no Web Playground](https://beagle-playground.netlify.app/#/demo/default-components/button.json)
