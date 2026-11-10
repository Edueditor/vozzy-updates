# Vozzy · plugin para Premiere Pro

Melhora a voz dos clipes da timeline: mede ruído, eco e volume, sugere o melhor motor (Reconstruir a voz, Só limpar ou os dois), deixa ouvir antes e depois e coloca o áudio tratado no lugar ou numa faixa abaixo.

Funciona no Mac (chip Apple, macOS 14+) e no Windows 10/11 (64 bits), com o Premiere Pro 2022 ou mais novo em qualquer idioma. Roda no seu computador, só no processador (CPU), sem internet depois da primeira vez.

## Instalar: escolha um jeito

### Jeito 1: pelo Claude (sem baixar nada)
Copie o link desta página e cole no Claude Code com a frase **"instala esse plugin para mim"**:

```
https://github.com/Edueditor/vozzy-updates
```

O Claude segue as instruções da seção *Para o Claude* abaixo. Ele instala o plugin e já prepara o motor.

### Jeito 2: pelo zip
1. Baixe o **[Vozzy_completo.zip](https://github.com/Edueditor/vozzy-updates/releases/latest/download/Vozzy_completo.zip)**. Ele traz o plugin, o guia em PDF e o atalho para o ZXP Installer.
2. Se ainda não tem o ZXP Installer, abra `Baixar ZXP Installer.html`.
3. Feche o Premiere, arraste o `Vozzy.zxp` para o ZXP Installer e abra o Premiere.
4. No Premiere, vá em **Window → Extensions → Vozzy** (em português: Janela → Extensões → Vozzy).
5. Na primeira vez, clique em **Preparar o motor** e espere (5 a 15 min, depende da internet).

Arquivos soltos: [Vozzy.zxp](https://github.com/Edueditor/vozzy-updates/releases/latest/download/Vozzy.zxp) · [Guia em PDF](https://github.com/Edueditor/vozzy-updates/releases/latest/download/Como_instalar_o_Vozzy.pdf) · [Baixar ZXP Installer](https://github.com/Edueditor/vozzy-updates/releases/latest/download/Baixar_ZXP_Installer.html)

Atualizações: o próprio plugin avisa quando sai uma versão nova.

### O motor (o que é baixado na primeira vez)
O Vozzy usa modelos abertos: **Sidon** (reconstrói a voz, MIT), **ClearerVoice MossFormer2** (limpa, Apache-2.0), **Silero VAD** e **DNSMOS** (MIT). Na primeira vez o painel instala tudo sozinho com o `uv`, que vem dentro do plugin. São uns **2,5 GB** no total:

| o quê | onde | tamanho |
|---|---|---|
| Python do painel (diagnóstico, controle) | Mac `~/Library/Application Support/Vozzy/env` · Win `%APPDATA%\Vozzy\env` | ~300 MB |
| Python base | `…/Vozzy/python` | ~70 MB |
| Motores (PyTorch só CPU + ClearerVoice) | `…/Vozzy/motor-env` | ~1 GB |
| Modelo Sidon | cache do Hugging Face (`~/.cache/huggingface`) | ~960 MB |
| Modelo ClearerVoice | `…/Vozzy/models/clearervoice` | ~210 MB |

Quem já tem o app **BESTX** no Mac não baixa os motores: o Vozzy usa os que já estão instalados nele.

---

## Para o Claude (instalação automática)
O usuário colou este link e quer o Vozzy instalado. Siga estes passos:
1. Baixe `https://github.com/Edueditor/vozzy-updates/releases/latest/download/Vozzy.zxp`. É um zip assinado.
2. Peça para o usuário fechar o Premiere Pro e espere ele confirmar.
3. Se já existir uma pasta antiga, apague-a. Depois extraia o conteúdo do `.zxp` sem alterar nenhum arquivo (a assinatura depende disso) em:
   - Mac: `~/Library/Application Support/Adobe/CEP/extensions/com.vozzy.premiere`
   - Windows: `%APPDATA%\Adobe\CEP\extensions\com.vozzy.premiere`
4. Não mexa na pasta de dados do plugin (Mac: `~/Library/Application Support/Vozzy`, Windows: `%APPDATA%\Vozzy`), a não ser para criar o motor no passo 5. Ali ficam o motor e o trabalho do usuário.
5. Prepare o motor (é o mesmo que o botão **Preparar o motor** faz; pule se a pasta `motor-env` já existir). Use o `uv` que vem no plugin, pasta `setup/`:
   - **Mac** (Terminal):
     ```bash
     EXT="$HOME/Library/Application Support/Adobe/CEP/extensions/com.vozzy.premiere"
     D="$HOME/Library/Application Support/Vozzy"; UV="$EXT/setup/uv-mac"
     chmod +x "$UV"; xattr -d com.apple.quarantine "$UV" 2>/dev/null
     export UV_PYTHON_INSTALL_DIR="$D/python" UV_CACHE_DIR="$D/cache" UV_PYTHON_PREFERENCE=only-managed
     "$UV" venv --python 3.12 "$D/env" && "$UV" pip install --python "$D/env/bin/python" -r "$EXT/setup/requirements-diag.txt"
     "$UV" venv --python 3.12 "$D/motor-env" && "$UV" pip install --python "$D/motor-env/bin/python" -r "$EXT/setup/requirements-motor-mac.txt"
     "$D/motor-env/bin/python" "$EXT/setup/baixar_modelos.py" "$D" && rm -rf "$D/cache"
     ```
   - **Windows** (PowerShell):
     ```powershell
     $EXT = "$env:APPDATA\Adobe\CEP\extensions\com.vozzy.premiere"; $D = "$env:APPDATA\Vozzy"; $UV = "$EXT\setup\uv.exe"
     $env:UV_PYTHON_INSTALL_DIR = "$D\python"; $env:UV_CACHE_DIR = "$D\cache"; $env:UV_PYTHON_PREFERENCE = "only-managed"
     & $UV venv --python 3.12 "$D\env"; & $UV pip install --python "$D\env\Scripts\python.exe" -r "$EXT\setup\requirements-diag.txt"
     & $UV venv --python 3.12 "$D\motor-env"; & $UV pip install --python "$D\motor-env\Scripts\python.exe" -r "$EXT\setup\requirements-motor-windows.txt"
     & "$D\motor-env\Scripts\python.exe" "$EXT\setup\baixar_modelos.py" "$D"; Remove-Item -Recurse -Force "$D\cache"
     ```
   O último comando termina com `ok · torch 2.11.0`. Se a internet cair, rode de novo: ele continua de onde parou.
6. Confira se `CSXS/manifest.xml` existe na pasta nova e diga qual versão ele mostra.
7. Avise o usuário para abrir o Premiere e ir em Window → Extensions → Vozzy.
