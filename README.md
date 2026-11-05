# DICCARLO TECH — Network Diagnostic (V1)

Ferramenta em Python para diagnosticar redes: abre um menu, executa testes, aponta problemas
com evidência e gera relatório técnico. Funciona em **Windows e Linux**, no PC ou **direto do pendrive**.


<img width="1256" height="444" alt="Captura_de_tela_20261104_093212" src="https://github.com/user-attachments/assets/f82a19de-01e4-44c1-abc2-311b7bd8317b" />


## Como executar

```bash
pip install -r requirements.txt     # psutil e rich
python main.py                      # menu no terminal
python main.py --gui                # interface gráfica (Tkinter)
python main.py --full               # diagnóstico completo, sem menu, salva TXT/JSON/HTML
python main.py --full --scan        # inclui varredura da sub-rede local (rede autorizada)
```

## Modo pendrive (sem instalar nada no PC)

1. Copie a pasta do projeto para o pendrive.
2. Uma vez, em qualquer PC com internet: `pip install -r requirements.txt --target libs`
   (a pasta `libs/` passa a ser usada automaticamente).
3. No PC do cliente: `iniciar_windows.bat` ou `./iniciar_linux.sh` (precisa de Python 3.9+ instalado).
4. Relatórios, histórico e configurações ficam na própria pasta (`reports/`, `config/`).

**Sem Python no PC do cliente (Windows):** rode `build_windows.bat` uma vez numa máquina Windows
com Python; ele gera `dist/DICCARLO-Network-Diagnostic.exe` (copie para o pendrive junto das
pastas `config/` e `reports/`). O .exe só pode ser gerado no Windows.

## O que a V1 faz

| Menu | Função |
|---|---|
| 1 | Diagnóstico completo (rede, ping, DNS, latência, ARP, análise) |
| 2 | Interfaces, IPv4, máscara, gateway, DNS e DHCP (quando identificável) |
| 3 | Ping: gateway, IPs externos e hosts informados |
| 4 | DNS: resolvedor do sistema + consulta direta a cada servidor configurado |
| 5 | Latência, jitter e perda de pacotes |
| 6 | Traceroute |
| 7 | Tabela ARP |
| 8 | Descoberta de dispositivos (IP, MAC, nome, fabricante, estado) |
| 9 | Portas TCP, serviços e observações básicas de segurança |
| 10 | Análise: CRÍTICO / ALTO / MÉDIO / BAIXO / NORMAL, com causa, impacto e sugestão |
| 11 | Relatórios TXT, JSON e HTML |
| 12 | Histórico |
| 13 | Configurações |

## Regras de segurança (embutidas no código)

- Varredura de dispositivos **só** em redes locais do equipamento ou listadas em
  `authorized_networks` (config/settings.json); sempre pede confirmação.
- Teste de portas apenas nos hosts/portas informados pelo operador, com confirmação. Só abre
  conexão TCP; não explora vulnerabilidades, não captura credenciais.
- Não altera configuração de rede, firewall ou proteções do sistema. Somente leitura.
- Nada é enviado a terceiros; funciona sem internet (testes externos apenas reportam falha).
- `Ctrl+C` (terminal) ou **Interromper** (GUI) param a operação.
- A análise só afirma problema com evidência. Se um utilitário (ex.: `ping`) não existe,
  o teste aparece como "não testado", nunca como falha de rede.

## Limitações conhecidas (honestas)

- **PDF**: previsto para a V3 (TXT, JSON e HTML já funcionam).
- **DHCP**: identificado no Windows (`ipconfig /all`) e no Linux com NetworkManager; nos demais
  casos aparece como "não identificável".
- **IP duplicado**: não detectado na V1 (exige sondagem ARP com Scapy/administrador).
- **Fabricante pelo MAC**: lista embutida reduzida. Para cobertura completa, crie
  `config/oui.txt` com linhas `AABBCC;Fabricante`.
- Descoberta usa ping + tabela ARP: dispositivos que bloqueiam ICMP só aparecem se já
  estiverem na tabela ARP.
- Traceroute depende de `traceroute`/`tracepath` no Linux (`tracert` já vem no Windows).
- Testado: Linux (Python 3.12) com 20 testes automatizados e a GUI sob Xvfb. **Ainda não
  testado em Windows real**; os parsers de `ipconfig`, `route` e `arp` foram validados com
  saídas de exemplo (inglês e português), não com uma máquina Windows.

## Testes

```bash
python -m unittest discover -s tests
```

## Estrutura

```
main.py                 ponto de entrada (CLI / GUI / --full)
core/                   Core Engine (sem dependência de interface)
  engine.py             orquestra os testes
  network.py connectivity.py dns.py latency.py scanner.py port_test.py
  analyzer.py           classificação de problemas
  formatting.py         formatação compartilhada (CLI, GUI e relatórios)
  reports.py history.py
interfaces/terminal.py  menu Rich
interfaces/graphical.py Tkinter
utils/                  system, permissions, settings
config/settings.json    limites, alvos, redes autorizadas
reports/                relatórios e history.json
tests/                  testes automatizados
```


