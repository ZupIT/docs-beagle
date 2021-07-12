---
title: Design System
weight: 121
description: >-
  Nesta seção, você encontra a descrição da classe Design System e detalhes dos
  métodos que a  implementa.
---

---

## Introdução

Você pode registrar estilos como fontes, cor, tamanho da fonte e componentes customizados com design system, que foi adicionado ao Beagle 1.0.0

É com o **design system** que se armazena os componentes de estilo registrados, criado no frontend e então a sua aplicação Android saberá qual componente de estilo deverá ser aplicado em cada tela server-drive. É por meio dos elementos de elementos da view são usados para criar sua tela. 

Para definir o design system da sua aplicação, você deve criar a classe que estenda um **Open  class** `DesignSystem`. Ela irá subscrever as funções que são usadas para aplicar os estilos aos componentes, e dentro de cada função, você pode declarar vários estilos. 

Cada estilo mostrado com o Beagle deve ser referenciado de uma forma que, cada um deles tenha um nome a ser identificado.

### Exemplo

O método da imagem registrada em uma série de imagens que será usada para construir páginas vindas do backend. O operador when é usado para designar os estilos, o primeiro valor listado é uma string que identifica o estilo do componente do Beagle \(muito similiar a key/value\) e o estilo do componente que foi listado com uma `Arrow Function ->`. 

O operador desta função **else**, irá conter o estilo padrão, como no código abaixo:


```kotlin
// This class is used by the RenderEngine in order to define the styles configured at the application.
// It must extend DesignSystem.
// These are the styles the AppDesignSystem implements: image, textStyle, inputTextStyle, buttonStyle, toolbarStyle and tabViewStyle

@BeagleComponent
class AppDesignSystem : DesignSystem() {

    override fun image(id: String): Int? {
        return when (name) {
            "delete" -> android.R.drawable.ic_delete
            "logo" -> android.R.drawable.ic_logo
            "banner" -> android.R.drawable.ic_banner
            else -> android.R.drawable.ic_menu_help
        }
    }
    
    override fun textStyle(id: String): Int? {
        return when (name) {
            "TextBlackBold" -> R.style.TextBlackBold
            else -> R.style.TextBlackBold
        }
    }
    
    override fun inputTextStyle(id: String): Int? {
        return R.style.TextBlack
    }

    override fun buttonStyle(id: String): Int? = R.style.ButtonBlack
    
    override fun toolbarStyle(id: String): Int? = R.style.Toolbar
    
    override fun tabViewStyle(id: String): Int? {
        return R.style.CustomTab
    }
}
```


{{% alert color="info" %}}
Quando uma classe estende o`Open Class DesignSystem` , é possível gerenciar estilos que serão aplicados aos componentes server-driven. É necessário implementar a classe para registrar os estilos no Beagle, porque somente assim você poderá ver os estilos e aplicá-los. 
{{% /alert %}}

Como um padrão Android, os estilos podem ser definidos no arquivo `style` e referenciados dentro da classe que entenda o Design System. 

```markup
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="TextBlackBold" parent="TextAppearance.AppCompat">
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">20sp</item>
        <item name="android:textStyle">bold</item>
    </style>

    <style name="TextBlack" parent="TextAppearance.AppCompat">
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">20sp</item>
    </style>

    <style name="ButtonBlack" parent="Widget.AppCompat.Button">
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">18sp</item>
        <item name="android:textAllCaps">false</item>
    </style>

    <style name="Toolbar" parent="Widget.AppCompat.Toolbar">
        <item name="android:background">@color/colorPrimary</item>
        <item name="navigationIcon">?attr/homeAsUpIndicator</item>
    </style>

</resources>
```
