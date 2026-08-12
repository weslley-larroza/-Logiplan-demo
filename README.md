# LogiPlan Pro — Otimização 3D de Carga (Demo)

Ferramenta web (HTML + JS + Plotly) para simular o **carregamento/cubagem de cargas** em veículos de transporte (carreta, Vanderleia, truck, rodo-trem) ou em espaços de armazenagem personalizados, com visualização 3D interativa e cálculo de aproveitamento de volume e peso.

Esta versão está configurada em **modo demonstrativo**: não depende de nenhum backend/API — os dados de exemplo são carregados localmente, para fins de apresentação e testes.
Link: https://weslley-larroza.github.io/-Logiplan-demo/
## Como funciona (fluxo de uso)

1. **Tela inicial** — a página carrega vazia, com o painel de configuração do veículo/espaço e a tabela de produtos sem itens.
2. **Carregar dados** — ao clicar em **"Carregar Exemplo"** (ou automaticamente, ao abrir a página), a tabela é populada com 3 itens de amostra (palete, caixas e palete vazio), cada um com suas dimensões, peso unitário e regras de empilhamento.
3. **Gerar Planejamento** — ao clicar em **"Gerar Planejamento"**, o algoritmo de cubagem calcula o posicionamento dos blocos dentro do espaço/veículo selecionado.
4. **Resultado** — o gráfico 3D exibe os blocos posicionados (ou fora dele, marcados como "sobra" quando não cabem), e o painel de estatísticas mostra pilhas alocadas, volume utilizado e peso total, com alerta visual caso o limite de peso do veículo seja excedido.

## Funcionalidades

- **Catálogo de veículos pré-configurados**: Carreta Simples, Carreta Vanderleia, Caminhão Truck e Rodo-trem (2 semirreboques), cada um com dimensões e limite de peso próprios, além de um modo "Espaço Personalizado" para armazém/depósito.
- **Tabela de produtos editável**: código, descrição, quantidade em unidades, capacidade por palete/pilha e limite de empilhamento físico.
- **Cálculo automático**: número de pilhas necessárias (`quantidade ÷ capacidade por pilha`, arredondado para cima) e peso total do bloco (`peso unitário × capacidade por pilha`).
- **Empacotamento 2D por área + limite de altura**: os blocos são posicionados em uma grade (resolução de 5 cm), tentando as duas orientações possíveis (na largura e rotacionada), respeitando a altura útil do veículo.
- **Rodo-trem**: distribuição sequencial da carga entre os dois semirreboques, com o excedente do primeiro sendo redirecionado automaticamente para o segundo.
- **Alertas de excesso de peso**: comparação do peso total carregado com o limite do veículo selecionado, com destaque visual (verde/amarelo/vermelho).
- **Detalhamento por item**: modal com as dimensões e peso do bloco de cubagem, e modal de edição para ajustar comprimento, largura, altura e peso unitário de cada produto.
- **Exportação de modelo CSV**: botão para baixar um arquivo `.csv` de exemplo com as colunas esperadas, útil como referência para integração futura com uma base de dados real.
- **Busca de produto simulada**: digitar um código conhecido (`P001`, `C002`, `PAL-A`, `CX-B`, `VAZIO`) no campo "CÓD." preenche automaticamente a linha a partir de um catálogo local (`DEMO_PRODUCT_CATALOG`), simulando a resposta de uma API real.

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Estrutura/estilo | HTML5 + CSS customizado (tema azul corporativo) |
| Componentes de UI | Bootstrap 5 + Bootstrap Icons |
| Visualização 3D | Plotly.js (`mesh3d` para os blocos, `scatter3d` para wireframe e rótulos) |
| Lógica de cubagem | JavaScript puro (sem dependências de build) |
| Tipografia | Google Fonts (Inter) |

Todas as bibliotecas são carregadas via CDN — não há etapa de build; basta abrir o arquivo `.html` em um navegador.

## Estrutura de dados de um item

```
{
  code: "PAL-A",              // código do produto
  description: "Palete...",   // descrição
  quantity_und: 10,            // quantidade total em unidades
  capacity_per_stack: 1,        // unidades por palete/pilha
  pallet_length_m: 1.20,        // comprimento do bloco (m)
  pallet_width_m: 1.00,         // largura do bloco (m)
  pallet_height_m: 1.75,        // altura de 1 palete/bloco (m)
  max_stack: 1,                 // máximo de paletes empilhados um sobre o outro
  und_weight_kg: 950            // peso por unidade (kg)
}
```

A partir disso, a aplicação calcula automaticamente:
- **Total de pilhas** = `ceil(quantity_und / capacity_per_stack)`
- **Peso do bloco** = `und_weight_kg × capacity_per_stack`
- **Empilhamento físico real** = `min(max_stack, altura do veículo ÷ altura do bloco)`

## Limitações do modo demonstrativo

- Não há conexão com banco de dados: os botões "Carregar Exemplo" e a busca por código usam dados fixos no próprio arquivo (`DEMO_PRODUCT_CATALOG` e a função `loadSampleRows()`).
- O algoritmo de empacotamento é uma heurística simples (grade + varredura), não um otimizador exato — ele prioriza os blocos mais altos/maiores primeiro, mas não garante o arranjo matematicamente ótimo.
- Para uso em produção, os pontos de integração a substituir são: `loadSampleRows()` → chamada real à API/banco, e `fetchProductDetails()` → consulta real por código de produto.


## Arquivos
Link: https://weslley-larroza.github.io/-Logiplan-demo/

- `index.html` — aplicação completa (autocontida).
- `README.md` — este arquivo.
