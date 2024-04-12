

Como um computador sabe o [[IP]] de um servidor através do DNS

1. O  computador faz um requisição para o `provedor de internet` ,  que tem o `recurse resolver`,  o provedor não sabe o  o endereço de `IP` do domínio mas sabe qual o `rootServer` responsável pelo `TLD` (Top-Level Domain) respectivo. ex: `.br`, `.com`, `.dev`
2. O `rootServer` não sabe o `ip` do servidor do domínio, porém ele sabe o `ip` do servidor que cuida do seu respectivo `TLD` , então o resolver recebe uma lista de IP  dos servidores que tem a lista de domínios e IPs  o `authoritavieServer` q
3. O `authoritavieServer` manda o IP do domínio para o resolver, e o resolver  faz a requisição  diretamente para os servidores do site/domínio com o IP

Todos esses dados ficam em cache para acelerar todo o processo, e a cada tempo se revalida