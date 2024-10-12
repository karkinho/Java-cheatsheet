# COmpilando o codigo

## Javac

## Java

# Utilização de database em mysql

## Instalar o connector

### JDBC

Conector desenvolvido em java para uso somente em aplicações java.

### ODBC

Conector desenvolvido para funcionar com qualquer linguagem de programação

## Conectando a uma database

Para fazer a conecção com uma database é nescessário criar uma variavel Connection para quardar a conecção aonde será realizada as ações, para conseguir a conecção se usa o DiverManager e sua função getConnection, passando o usuário e senha de acesso para o banco de dados, você tem que tratar exceções nesse codigo.

```JAVA
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Connection;

try {
    Connection conn;
    conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/DATA_BASE","USER","PASSWORD");
} catch (ClassNotFoundException ex) {
    System.out.println("driver não está disponível para acesso ou não existe");
}
```

Tambem é possivel passar as informações de conecção atraves de Strings, boa prática.

```JAVA
public String url = "jdbc:mysql://localhost:3306/exemplo_senac";
public String user = "root";
public String password = "abcd1234";

conn = DriverManager.getConnection(url, user, password);

```

## Desconectando de uma database

Desconectar do banco de dados é apontar para a variavel de conecção e utilizar o .close() nela.

```JAVA
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Connection;

try {
    conn.close();
} catch (SQLException ex) {

}
```

## Relizando comandos

Os comando abaixo seram utilizados em Statements ou PreparedStatement.

```JAVA
.executeUpdate(COMAND); // em caso de delete, update, insert
.executeQuery(COMAND); // em caso de select
```

A diferença de um Statement e um PreparedStatement é a capacidade do Prepared de ter informações parametrizadas.

### Statement

Para Utilizar o statemet se cria uma variavel de mesmo nome e se coloca o valor nela de a conecção a ser utilizada com a função .creatStatement(), que criara efetivamente o statement, depois se executa uma das funções de execução no statement passando o comando de sql por uma string.

```JAVA
import java.sql.Statement;

    Statement stmt = null;
    String comand = "insert into alunos (nome, idade, endereco) values ('Ana','23','Rua 7 de setembro 826')";

try{

    stmt = conn.createStatement();
    stmt.executeUpdate(comand);
    System.out.println("Dados inseridos.");
}
catch (SQLException sqle ){
    System.out.println( "Erro inserindo : " + sqle.getMessage() );
}
```

### PreparedStatement

Se faz a criação igual ao Statement contudo se passa o comando já na função de criação do PreparedStatement, essa função terá interogações nos locais das variaveis que são parametrizadas, para definilas se utiliza set no PreparedStatement nesse formato: .setTYPE(QUAL POSIÇÂO DA INTEROGAÇÂO, VALOR); as posições das interogações começão em 1, e depois se executa a função para executar o comando.

```JAVA
import java.sql.PreparedStatement;

PreparedStatement ps = null;
String sql = "insert into alunos values (?,?,?,?)";

try {
    ps = conn.prepareStatement(sql);
    ps.setInt(1,2);
    ps.setString(2, "Érico");
    ps.setInt(3,30);
    ps.setString(4, "Rua das araucárias 20");

    ps.executeUpdate();

    System.out.println( " Dados inseridos com sucesso." );
} catch ( SQLException sqle ) {
    System.out.println( "Erro no acesso ao Bando de Dados : "+ sqle.getMessage());
}
```

### ResultSet

Utilizado para receber informações do banco de dados, ele receberá um valor vindo de um Statement ou PreparedStatement, e utilizando .next() ciclaremos entre todos os resultados,
e utilizando os ResultSet.getTYPE("NOME DA COLUNA DA DATABASE"); se pode pegar as informações da query.

```JAVA
import java.sql.Statement;
import java.sql.ResultSet;

    Statement stmt = null;

try {
    String sql = "select id,nome,idade,endereco from alunos" ;
    ResultSet rs = stmt.executeQuery(sql);

    while (rs.next()) {
        int id = rs.getInt("id");
        String nome = rs.getString("nome");
        int idade = rs.getInt("idade");
        String endereco = rs.getString("endereco");

        System.out.println("dados da tabela autor do banco de dados");
        System.out.println("--------------------------");
        System.out.println(id + " - " + nome + " - "+ idade+ " - "+ endereco);
    }
} catch (SQLException sqle) {
    System.out.println( "Erro efetuando consulta : " + sqle.getMessage() );
}
```

## Padrão DAO (data access object)

Esse padrão visa separar a parte de conecção e CRUD do codigo do resto, criando uma classe da tabela a ser trabalhada de mesmo nome aonde contem todas as variaveis contidas na tabela, e uma classe de operação sobre o banco que terá o nome do nome da tabela mais DAO ao lado, nela será realizada todas as funções de CRUD tanto quanto a conecção e desconecção do bando de dados.
