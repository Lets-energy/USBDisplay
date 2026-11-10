# MSDisplay 2.0.1.3 Fullscreen (versão modificada, não oficial)

🇬🇧 [English](README.md) | 🇧🇷 Português

Versão modificada do **MSDisplay Android 2.0.1.3** (dongle USB → RCA/CVBS, chips MacroSilicon MS2160/MS9120) que **preenche a tela traseira** em multimídias Android com tela 16:9, em vez de mostrar a imagem pequena, com barras pretas ao redor.

> **Aviso:** projeto da comunidade, sem vínculo com MacroSilicon, UltraSemi ou MindShow. Use por sua conta e risco. O APK é distribuído como está, sem garantia.

## O problema

O app original captura a tela da multimídia em um quadro fixo de 720x480 (ou 720x576). O Android encaixa a tela da multimídia (por exemplo, 1024x600) dentro desse quadro **mantendo a proporção**, e sobram barras pretas. Na tela de encosto, a imagem aparece menor do que o espaço disponível.

## O que muda nesta versão

1. **Captura na proporção real da multimídia.** O `VirtualDisplay` e o `ImageReader` passam a usar a largura de saída (720) e uma altura calculada a partir da resolução real da tela (1024x600 → 720x422), sem barras.
2. **Esticamento para 720x480.** Cada quadro é esticado na vertical (cópia de linhas, sem reamostragem) para 720x480 antes de seguir pelo USB. O custo de CPU é baixo.
3. **Padrão 480/60 Hz para saída CVBS.** Sem configuração salva, o app usa 720x480 a 60 Hz em vez de 720x576 a 50 Hz. A opção continua escolhível na tela de configurações.

Alterações no código (smali, a partir do APK 2.0.1.3):
- `CaptureService`: novos campos `capHeight` e `sb`; `getSize()` calcula `capHeight`; `displayInit()` usa `capHeight`; `onImageAvailable()` chama o esticamento e envia o quadro compacto (stride 0); padrão do timing CVBS.
- Nova classe `com.ms.ms2160.service.Stretch`, com `capH(...)` e `stretch(...)`.

O nome do pacote (`com.ms.ms2160`) **não foi alterado**.

## Limitações

- A saída continua sendo **RCA/CVBS (analógico), no máximo 720x480**. A tela traseira amplia essa imagem para a resolução dela. Fica cheia, mas mais suave do que uma imagem nativa, e textos pequenos têm pouca definição.
- Use a tela traseira em modo **Wide / Full** (botão de aspecto do controle ou menu OSD). Em 4:3 a imagem sai deformada.
- Se a imagem parecer esticada demais, abra uma issue: uma alternativa é cortar a altura em vez de esticar.

## Instalação

1. **Desinstale o MSDisplay original.** O APK desta versão é assinado com outra chave, e o Android recusa a instalação por cima ("não foi possível instalar a atualização"). Se aparecer "Desinstalar atualizações", use essa opção e depois desinstale de novo.
2. Instale `MSDisplay_2.0.1.3_fullscreen.apk` (baixe na aba **Releases**).
3. Conceda de novo as permissões: sobreposição de tela e gravação de tela.
4. Plugue o dongle e ajuste o aspecto da tela traseira para Wide/Full.

Para voltar à versão original, desinstale esta e reinstale `MSDisplay_Android_V2.0.1.3.apk` do repositório MindShow/USBDisplay.

## Testado em

- Multimídia Android com tela de 1024x600 e tela traseira com entrada RCA. A versão instala e funciona.
- Outros aparelhos: resultados são bem-vindos nas issues (modelo da multimídia, versão do Android, resolução, e se as barras sumiram).

## Verificação

```
sha256sum MSDisplay_2.0.1.3_fullscreen.apk
```

SHA-256: ED1017B6563B786F025737297E65F45756D3E047CCF3B5FF35C7DC96FD6D7CBE

## Créditos e licença

- Base: [MindShow/USBDisplay](https://github.com/MindShow/USBDisplay) (MacroSilicon / UltraSemi), arquivo `Android/MSDisplay_Android_V2.0.1.3.apk`.
- O repositório original não indica licença. Esta versão é uma modificação feita por engenharia reversa do APK, sem acesso ao código-fonte do app, publicada para uso pessoal e experimentação. Se os detentores dos direitos pedirem a remoção, ela será removida.
