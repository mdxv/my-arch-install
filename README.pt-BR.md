
# my-arch-install
📔 A little guide to install the Arch Linux for me

## Teclado
Primeiro cheque se o meu teclado está no layout correto, por que por padrão a Instalação do Arch vem com o teclado em layout en-US.

Para trocar o layout use o seguinte comando:

```bash
loadkeys br-abnt2
```

## Conexão com a Internet
Normalmente já utilizo o meu computador diretamente cabeado, porém há alguns casos onde precisarei utilizar o Wi-Fi, nesse caso eu utilizo o pacote [iwd](https://wiki.archlinux.org/title/iwd).
Para utilizar inicie com o comando:
```bash
iwctl
```
Descubra o nome do dispositivo de rede Wi-Fi que está em seu computador com o comando:
```
[iwd]# device list
```

<div align="center">
  <img src="https://i.imgur.com/GGxULsZ.png">
</div>

Agora que sabemos que nossos dispositivo se chama **wlan0**
Para procurar as redes perto utilizamos:
```
[iwd]# station wlan0 scan
```