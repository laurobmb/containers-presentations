# Containers Presentations

Trilha de 3 apresentações técnicas sobre containers, do terminal do desenvolvedor até a plataforma corporativa, criada para a **Red Hat Academy** e apresentada para turmas de **Engenharia de Software / Análise e Desenvolvimento de Sistemas / Ciência da Computação**.

## Motivação

Para a maioria dos estudantes, "container" é sinônimo de `docker run` ou `podman run`: um comando que baixa uma imagem e "mágicamente" faz um app funcionar. Essa camada de abstração é ótima para produtividade, mas esconde justamente o que um(a) futuro(a) engenheiro(a) de software precisa entender para depurar problemas reais em produção — por que um container falha com *"permission denied"* mesmo com a pasta em `777`, por que um processo é matado sem aviso (OOM), ou por que a mesma imagem se comporta diferente em um cluster com centenas de nós.

Esta trilha nasceu da necessidade de conectar três camadas que normalmente são ensinadas de forma isolada:

1. **A ferramenta que o dia a dia usa** (Podman) — o que o desenvolvedor realmente digita no terminal.
2. **O mecanismo do Sistema Operacional por trás dela** (Namespaces, cgroups v2, SELinux, Seccomp) — o que faz um container ser, de fato, apenas um processo Linux isolado, e não uma "mini VM".
3. **A escala corporativa** (Kubernetes e Red Hat OpenShift) — como esses mesmos mecanismos de kernel são replicados e automatizados quando o problema deixa de ser "uma máquina" e passa a ser "uma frota de máquinas".

A ideia central é mostrar que **não existe mágica**: o mesmo `container_t` do SELinux que protege um `podman run` na sua máquina é o que protege um Pod em um cluster OpenShift de produção. Entender o Módulo 2 (Kernel) é o que transforma um usuário de Podman/Docker em alguém capaz de diagnosticar e justificar decisões de arquitetura em ambientes containerizados — uma habilidade cada vez mais central em vagas de desenvolvimento, DevOps e SRE.

## Estrutura da trilha

A ordem foi desenhada para ser sequencial — cada módulo assume o conhecimento do anterior:

| # | Apresentação | Foco |
|---|---|---|
| 1 | [`Introdução a Containers e Podman.html`](./Introdução%20a%20Containers%20e%20Podman.html) | VMs vs. Containers, padrão OCI, por que o Podman é *daemonless*/*rootless*, CLI essencial e o conceito de Pods. |
| 2 | [`anatomia_de_containers_kernel_cgroups_e_selinux.html`](./anatomia_de_containers_kernel_cgroups_e_selinux.html) | Namespaces, cgroups v2, SELinux/MCS (`:z`/`:Z`), Seccomp/Capabilities e o modelo *rootless* na prática. |
| 3 | [`do_kernel_ao_openshift_a_jornada_dos_containers_corporativos.html`](./do_kernel_ao_openshift_a_jornada_dos_containers_corporativos.html) | Como o Podman escala para Kubernetes (CRI-O/crun) e se consolida no Red Hat OpenShift: RHCOS, MCO, Operators/OLM, SCCs e o ecossistema GitOps/Pipelines. |

Um `index.html` serve como capa/índice, com links diretos para as três apresentações.

## Como visualizar

Basta abrir o `index.html` (ou qualquer um dos três arquivos individualmente) direto no navegador — são apresentações estáticas em HTML/Tailwind, sem necessidade de build ou servidor.

- Navegue com as setas `←` `→` ou `Espaço`.
- `F` alterna para tela cheia (ideal para projeção em sala de aula).

## Público-alvo

Turmas de graduação em Engenharia de Software, ADS e Ciência da Computação, com conhecimento prévio básico de Linux e linha de comando.
