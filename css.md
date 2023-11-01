---
title: "CSS"
order: 3
in_menu: true
---
## Typographie

### Ajuster la taille des caractères cyrilliques

On peut avoir plusieurs alphabets sur un même site. Pour que l'ensemble des caractères restent harmonieux en terme de taille, je déclare une font uniquement pour les [caractères cyrilliques](https://en.wikipedia.org/wiki/Cyrillic_(Unicode_block)) et j'ajuste leur taille avec [`size-adjust`](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/size-adjust) un peu au doigt mouillé pour que ça s'intègre bien avec ma font de base.


```css
@font-face {
  font-family: "Ajusted Cyrillic";
  size-adjust: 95%;
  src: local(arial);
  unicode-range: U+04??; // Cyrillic characters
}

.ajusted-cyrillic {
  font-family: "Ajusted Cyrillic", Arial, serif;
}
```