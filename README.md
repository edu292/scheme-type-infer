# scheme-type-infer
Um motor de inferência de tipos simples e poderoso para um subconjunto da linguagem Scheme.

## Demonstrativo
O analisador inspeciona a definição de uma função e infere os tipos de seus parâmetros com base em como eles são utilizados com operadores primitivos.

Exemplo de Inferência Básica:

```scheme
;; ENTRADA
(inferred-types 
  '(define (calculate-area width height factor) 
     (+ (* width height) factor)))

;; SAÍDA
'((width number) (height number) (factor number))
Exemplo com Conflito de Tipos:
```

O analisador detecta quando uma variável é usada de maneiras conflitantes.

```scheme
;; ENTRADA
(inferred-types 
  '(define (conflicting-use data)
     (sentence data (+ data 5))))

;; SAÍDA: 'x' indica um tipo conflitante
'((data x))
```

Exemplo com Tipo Desconhecido:

Se um parâmetro não for utilizado com um operador primitivo, seu tipo permanece desconhecido.

```scheme
;; ENTRADA
(inferred-types 
  '(define (unused-param config value)
     (+ value 10)))

;; SAÍDA: '?' indica um tipo não inferido
'((config ?) (value number))
```

## 🎯 Sobre o Projeto
Este projeto é uma implementação de um analisador de inferência de tipos para a linguagem de programação Scheme. Nascido a partir de um exercício do renomado curso CS61A (Structure and Interpretation of Computer Programs), o projeto foi expandido por puro interesse em explorar os mecanismos internos de sistemas de tipos em linguagens dinâmicas.

O objetivo é ir além da implementação e investigar um dilema central da engenharia de software: o *trade-off* entre a **flexibilidade do desenvolvedor** e a **otimização da máquina**.

Linguagens dinâmicas como Scheme, Python e JavaScript oferecem grande **praticidade e dinamicidade**. Elas aceleram a prototipagem e reduzem o código boilerplate ao não exigirem a declaração estática de tipos. No entanto, essa flexibilidade tem um custo, especialmente em codebases maiores:
- **Performance**: A verificação de tipos é adiada para o tempo de execução, introduzindo uma sobrecarga a cada operação.
- **Segurança e Robustez**: Erros de tipo (`TypeError`) são descobertos em tempo de execução em vez de serem capturados durante a compilação.

Este projeto simula o primeiro passo que todo ambiente de execução de uma linguagem dinâmica deve dar: **descobrir a intenção do programador e inferir os tipos a partir do contexto**, um processo que serve de base para todas as otimizações subsequentes. Ele também serve como um excelente exemplo do poder da homoiconicidade de Lisp/Scheme, onde a sintaxe (código como estrutura de dados) permite uma poderosa manipulação de código de forma elegante.

## 🔬 Contexto Teórico: O Dilema dos Tipos
Mesmo que o programador não defina os tipos, o computador fundamentalmente precisa deles. Uma CPU executa instruções de máquina distintas para somar dois inteiros e para somar dois números de ponto flutuante. A responsabilidade de preencher essa lacuna recai sobre o runtime da linguagem, que pode adotar diferentes estratégias:

1. Interpretadores Puros
A abordagem mais simples. O interpretador verifica os tipos de cada variável toda vez que uma operação é executada. É robusto, mas inerentemente lento, pois o mesmo trabalho de verificação é repetido em cada iteração de um loop, por exemplo. O scheme-type-infer é um modelo conceitual deste processo de verificação.

2. Compiladores JIT (Just-In-Time)
A solução moderna para a performance de linguagens dinâmicas (ex: V8 no JavaScript, PyPy). Um JIT atua como um otimizador adaptativo:
- Ele começa interpretando o código.
- Monitora o código em execução ("hot spots") e observa os tipos que são passados na prática.
- Com base nessa observação, ele compila uma versão otimizada daquele trecho de código em linguagem de máquina nativa, assumindo que os tipos permanecerão os mesmos.
- Se essa suposição falhar (uma função que sempre recebia inteiros de repente recebe uma string), o JIT descarta o código otimizado ("de-optimization") e volta para o modo de interpretação mais lento.

3. Compiladores Estáticos (AOT - Ahead-of-Time)
Em linguagens como C++, Java ou Rust, os tipos são conhecidos em tempo de compilação. Isso permite ao compilador gerar o código de máquina mais otimizado possível desde o início. Ele pode realizar otimizações profundas, como inlining de funções e alocação de memória na stack, pois não há ambiguidade sobre o tamanho ou o layout dos dados.

Este projeto, portanto, explora a pedra fundamental sobre a qual as estratégias de JIT e interpretadores são construídas: a capacidade de entender o código para inferir tipos.

## 🛠️ Tecnologias Utilizadas
- Linguagem: Scheme (R5RS)
- Ambiente: Racket (utilizando a biblioteca `#%require simply-scheme`)

## ✨ Principais Funcionalidades
- Inferência de Tipos: Analisa o corpo de uma função para determinar os tipos dos parâmetros (`number`, `sentence-or-word`, `list`, `procedure`).
- Detecção de Conflitos: Identifica variáveis que são usadas com operadores de tipos incompatíveis, marcando-as com `x`.
- Análise Recursiva: Navega por expressões aninhadas (sub-expressões) para coletar todas as informações de tipo.
- Identificação de Tipos Desconhecidos: Marca parâmetros cujo tipo não pôde ser inferido com `?`.

## 🚀 Instalação e Uso
Para executar este analisador, você precisará de uma instalação do Racket.

Clone o repositório:

```bash
git clone https://github.com/edu292/scheme-type-infer.git
cd scheme-type-infer
```
Ou simplesmente salve o código-fonte em um arquivo, por exemplo, infer.scm.

Inicie o REPL do Racket:
Abra o terminal e execute o Racket:

```bash
racket
```

Carregue o arquivo:
Dentro do REPL do Racket, carregue o arquivo contendo o código:

```scheme
(load "infer.scm")
```

Execute a análise:
Chame a função inferred-types com uma definição de função "quotada" (precedida por ') como argumento:

```scheme
> (inferred-types 
    '(define (example x y) 
       (+ x (max y 5))))

'((x number) (y number))
```

## 🧠 Desafios e Aprendizados
O desenvolvimento deste projeto foi uma jornada de aprendizado sobre a complexidade por trás da simplicidade aparente das linguagens dinâmicas.

O maior desafio técnico foi **gerenciar a recursão através dos diferentes níveis de aninhamento do código (AST)**, diferenciando corretamente entre sub-expressões a serem analisadas e operadores `built-in` que serviam como base para a inferência.

O principal aprendizado foi solidificar a compreensão de como sistemas de tipos em tempo de execução são estruturados. Este projeto proporcionou uma **visão prática do primeiro e mais crucial passo no ciclo de vida da execução em uma linguagem dinâmica**, um processo que serve de base para desde os interpretadores mais simples até os mais sofisticados compiladores JIT.

Ficou claro que a recursão, especialmente no contexto de uma sintaxe homoicônica como a de Lisp, é uma ferramenta extraordinariamente poderosa para a navegação e manipulação de estruturas de dados complexas, incluindo o próprio código-fonte.

## 📜 Licença
Este projeto está sob a licença MIT. Veja o arquivo LICENSE para mais detalhes.
