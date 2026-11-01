# Finanças Pessoais Specification

Este arquivo é uma especificação de como gerar o app para gerenciamento de controle financeiro pessoal.

Nome do app: Finanças Pessoais
Descrição: Controle financeiro pessoal

## Requisitos

- Todo app precisa ser implementado no frontend usando somente HTML, CSS e JavaScript;
- O app terá somente uma página `index.html` com CSS e javascrpt embutido nela;
- Não use frameworks como Angular, React ou Vue;
- Sempre use fontes como Roboto, Helvetica, etc. Não use fontes que divirja do design Material UI;
- Sempre use icones de libs gratúitas como Material Symbols, Font Awesome ou Bootstrap Icons;
- Por padrão o app terá o idioma Português do Brasil;
- Sempre implemente o layout responsivo para funcionar em desktop e mobile;
- O app usará o File System Access API para manter a persistência do app e salvar os dados do usuário utilizador;
- O arquivo persistido será um JSON no formato da interface `AppDataFile` abaixo:

```
interface BankStatement {
    description: string;
    value: string; // ex: -101.99
    bankCode: string;
    bankInfo: string;
    createdAt: Date;
}

interface AppDataFile {
    version: string; // ex: "1.0.0"
    statement: BankStatement[];
}
```

### Página Main Template

- Precisa ter uma toolbar no topo com um menu sanduíche para mostrar/ocultar o aside de navegação;
- A toolbar de topo precisa ter o nome da aplicação;
- A toolbar de topo terá um botão no lado direito para alterar entre o modo dark e light;
- Precisa ter um aside no lado esquerdo usado para navegar entre as páginas da aplicação;
- O conteúdo principal será exibido as outras páginas da aplicação;

### Página Inicial

- Será acessível através de um item no menu do aside com nome `Importação de extratos`;
- Essa página tera um input de entrada para upload de arquivos de extratos no formato CSV;
- O conjunto de campos (description, value, bankCode, bankInfo, createdAt) devem ser únicos e não pode ser persistido dados duplicado;

#### Formato de CSV do Bradesco:

- O arquivo csv válido para importação começa com uma linha com informações da conta, uma outra linha com os cabeçalhos e as próximas linha são os dados úteis. Ignore as linhas finais fora do padrão;
- Os dados do arquivo é separado por ";";
- Os campos #{credit} e #{debit} deve ser salvo como value, sendo que nunca os dois aparecem na mesma linha. Quando for debit adicione o simbolo de menos (-) antes do valor;
- Os campos de #{credit} e #{debit} usam o padrão brasileiro de moeda "1234,99" e devem ser convertidos antes de salvar;

```
Extrato de: #{bankInfo};;;;;
Data;Histórico;Docto.;Crédito (R$);Débito (R$);Saldo (R$)
#{createdAt};#{description};#{bankCode};#{credit};#{debit};#{balance}
```

#### Formato de CSV do Nubank:

- O arquivo csv válido para importação começa com uma linha de cabeçalhos e as próximas linha são os dados úteis;
- Os dados do arquivo é separado por ",";
- O campo #{value} já vem no formato certo com simbolo de menos quando debito e usando o padrão "1234.99";

```
Data,Valor,Identificador,Descrição
#{createdAt},#{value},#{bankCode},#{description}
```

- O arquivo CSV não tras informações sobre bankInfo, então salve esse campo com valor "nubank";

### Página Dashboard

- Será acessível através de um item no menu do aside com nome `Dashboard`;
- Terá um filtro de periodo com dois input date: Data inicial e Data final;
- O filtro de perido controla a quantidade de barras que aparecerá no gráfico;
- Terá um gráfico de barras usando chart.js com o balanço mensal;
- Checkbox "Exibir somente balanço" para alterar o modo do gráfico;
- Modo padrão do gráfico:
    - Uma barra verde com o somatório mensal dos valores positivos (crédito) do mês e uma barra vermelha com o somatório mensal dos valores negativos (débito), porém o somatório dos valores negativos aparecerá com valor absoluto;
    - Nesse modo o gráfico só exibe valores positivos. Os negativos aparecem como valor absoluto, mas com cor vermelho;
- Modo balanço do gráfico:
    - O gráico só terá esse comportamento quando o checkbox estiver marcado;
    - Uma barra única azul exibe a diferença do somatório de valores mensal;
    - Por exemplo, 12 meses exibe 12 barras com o somatorio do mês. Caso o somatório do débito for maior que o somatório do crédito então a barra terá valor negativo;
    - Nesse modo o gráfico pode exibir valores negativos;
- Os filtros so serão aplicados depois de clicar no botão "Aplicar";

### Página de Consulta de Extrato

- Será acessível através de um item no menu do aside com nome `Consulta de Extrato`;
- Essa página implementará um CRUD do modelo de dados `BankStatement`;
- O CRUD terá uma tabela que deve ocupar 100% da largura do conteudo principal;
- A tabela deve ser paginada, ordernada por colunas e com filtros de consulta por coluna;
- O campo createdAt deve ser um filtro de periodo com data inicial e final;
- Deve existir opção paginar por 5, 10, 20, 50, 100 itens;
- Os filtros so serão aplicados depois de clicar no botão "Consultar";
- Os headers das colunas terão um icone de seta indicando o status de ordernação;