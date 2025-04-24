# ProjetoForms1.FrmFornecedores
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Data.SqlClient;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace ProjetoForms1
{
    public partial class FrmFornecedores : Form
    {
        public FrmFornecedores()
        {
            InitializeComponent();
            AtualizarGrid();
        }
        //Criar a instância do SQLConnection
        SqlConnection con = new SqlConnection(Dados.Conexao);
        private void btnInserir_Click(object sender, EventArgs e)
        {
            try
            {
                //validar os campos: cpf,nome,tel e cep.
                //se não estão preenchidos-enviar msgbox
                if (txtNome.Text == ""||txtCNPJ.Text=="") //ou||
                {
                    MessageBox.Show("Preencha o campo obrigatório!",
                        "Sistema TI35", MessageBoxButtons.OK,
                        MessageBoxIcon.Exclamation);
                }
                else
                {
                    //processo de inserir cliente novo
                    //abertura do banco
                    con.Open();
                    //atribuição da instrução do insert na variável sql
                    string sql = "Insert into fornecedor(nome,cnpj,telefone)values(@nome,@cnpj,@telefone)";
                    //para executar a instrução acima, é necessário executador
                    SqlCommand cmd = new SqlCommand(sql, con);
                    cmd.Parameters.Add("@nome", SqlDbType.VarChar).Value = txtNome.Text;
                    cmd.Parameters.Add("@cnpj", SqlDbType.VarChar).Value = txtCNPJ.Text;
                    cmd.Parameters.Add("@telefone", SqlDbType.VarChar).Value = txtTelefone.Text;
                    //se todos os campos estiverem ok, vai executar a instrução no banco
                    cmd.ExecuteNonQuery();
                    //fechar conexão
                    con.Close();

//vai limpar os campos
LimparTudo();

//Atualizar a grid
AtualizarGrid();

//mensagem de confirmação ok
MessageBox.Show("Fornecedor ok!", "Sistema Ti35", MessageBoxButtons.OK,
MessageBoxIcon.Information);
}

}
catch (SqlException erro)
{
MessageBox.Show("Erro ao inserir marca: " + erro.Message);
}
}
private void LimparTudo()
{
txtCodigo.Clear();
txtCNPJ.Clear();
txtNome.Clear();
txtTelefone.Clear();
txtNome.Focus();
}

private void btnLimpar_Click(object sender, EventArgs e)
{
LimparTudo();
}
public void AtualizarGrid()
{
try
{
dgFornecedor.DataSource = listaFornecedor();

//configuração do cabeçalho do dgCliente
dgFornecedor.Columns[0].HeaderText = "Cód.";
dgFornecedor.Columns[1].HeaderText = "Nome";
dgFornecedor.Columns[2].HeaderText = "CNPJ";
dgFornecedor.Columns[3].HeaderText = "Telefone";

//configuração da largura do dgCliente
dgFornecedor.Columns[0].Width = 80;
dgFornecedor.Columns[1].Width = 200;
dgFornecedor.Columns[2].Width = 250;
dgFornecedor.Columns[3].Width = 150;

//configuração de permissão
dgFornecedor.SelectionMode =
DataGridViewSelectionMode.FullRowSelect;
dgFornecedor.AllowUserToDeleteRows = false;
dgFornecedor.AllowUserToAddRows = false;
dgFornecedor.ReadOnly = true;
}
catch (SqlException erro)
{
MessageBox.Show("Erro: " + erro.Message);
}
}
public static DataTable listaFornecedor()
{
try
{
SqlConnection con = new SqlConnection(Dados.Conexao);
con.Open();
string sqlListar = "Select * from fornecedor";
//vamos utilizar uma classe adaptador para receber os dados da tabela cliente.
SqlDataAdapter da = new SqlDataAdapter(sqlListar, con);
//estamos chamando uma classe do tipo Tabela
DataTable dt = new DataTable();
da.Fill(dt);
return dt;
con.Close();
}
catch (SqlException erro)
{
return null;
}
}

private void btnAlterar_Click(object sender, EventArgs e)
{
try
{
//declarar uma variável do tipo DialogResult e receber a resposta se deseja alterar.
DialogResult resp = MessageBox.Show("Deseja realmente alterar?",
"Sistema TI35", MessageBoxButtons.YesNo,
MessageBoxIcon.Question);
if (resp == DialogResult.Yes)
{
//vai efetuar as alterações daquele cliente em questão
FornecedorInformation f = new FornecedorInformation();
f.Codigo = Convert.ToInt32(txtCodigo.Text);
f.Nome = txtNome.Text;
f.CNPJ = txtCNPJ.Text;
f.Telefone = txtTelefone.Text;

//abrir o banco
con.Open();
//instrução sql para alterar
string sqlAlterar = "Update fornecedor set nome=@nome,cnpj=@cnpj,telefone=@telefone where " +
"codigo=@codigo";
SqlCommand cmdAlterar = new SqlCommand(sqlAlterar, con);
cmdAlterar.Parameters.Add("@nome", SqlDbType.VarChar).Value =
f.Nome;
cmdAlterar.Parameters.Add("@cnpj", SqlDbType.VarChar).Value =
f.CNPJ;
cmdAlterar.Parameters.Add("@telefone", SqlDbType.VarChar).Value =
f.Telefone;
cmdAlterar.Parameters.Add("@codigo", SqlDbType.Int).Value =
Convert.ToInt32(f.Codigo);

//se tudo estiver ok, vai executar a query
cmdAlterar.ExecuteNonQuery();
con.Close();
AtualizarGrid();
LimparTudo();
}
}
catch (SqlException erro)
{
MessageBox.Show(erro.Message);
}
}

private void dgCliente_MouseClick(object sender, MouseEventArgs e)
{
try
{
//vai capturar qual é a linha selecionada e qual é o código do cliente em questão.

//identificar qual é a linha clicada
int linha = dgFornecedor.SelectedRows[0].Index;
//MessageBox.Show(""+linha);

//analisar a linha clicada
if (linha >= 0)
{
//precisa saber qual é o código do cliente ref. a linha clicada.
int codigo = Convert.ToInt32
(dgFornecedor.Rows[linha].Cells[0].Value);
//MessageBox.Show("Linha: " + linha + " e Código: " + codigo);
//acesso ao método selecionarCliente, onde fará a busca pelo código.
FornecedorInformation f = selecionarFornecedor(codigo);
txtCodigo.Text = f.Codigo.ToString();
txtNome.Text = f.Nome.ToString();
txtCNPJ.Text = f.CNPJ.ToString();
txtTelefone.Text = f.Telefone.ToString();
}
}
catch(SqlException erro)
{

}
}
public static FornecedorInformation selecionarFornecedor(int codigo)
{
try
{
SqlConnection con = new SqlConnection(Dados.Conexao);
con.Open();
string sqlSelecionar = "Select * from fornecedor where codigo=@codigo";
SqlCommand cmd = new SqlCommand(sqlSelecionar, con);
cmd.Parameters.Add("@codigo", SqlDbType.Int).Value = codigo;
//como precisa trazer apenas as inf´s do código específico, ele vai varrer os registros e trazer somente o que for válido.
SqlDataReader dr = cmd.ExecuteReader();
//se houver dados para efetuar a busca, vai varrer até o último reg.
if (dr.Read())
{
FornecedorInformation f = new FornecedorInformation();
f.Codigo = Convert.ToInt32(dr[0]);
f.Nome = dr[1].ToString();
f.CNPJ = dr[2].ToString();
f.Telefone = dr[3].ToString();

con.Close();
return f;
}
else
{
con.Close();
return null;
}

}
catch (SqlException erro)
{
return null;
}
}

private void txtPesquisaNome_TextChanged(object sender, EventArgs e)
{
try
{
dgFornecedor.DataSource = pesquisaFornecedor(txtPesquisaNome.Text);

//configuração do cabeçalho do dgCliente
dgFornecedor.Columns[0].HeaderText = "Cód.";
dgFornecedor.Columns[1].HeaderText = "Nome";
dgFornecedor.Columns[2].HeaderText = "CNPJ";
dgFornecedor.Columns[3].HeaderText = "Telefone";

//configuração da largura do dgCliente
dgFornecedor.Columns[0].Width = 80;
dgFornecedor.Columns[1].Width = 200;
dgFornecedor.Columns[2].Width = 250;
dgFornecedor.Columns[3].Width = 150;

//configuração de permissão
dgFornecedor.SelectionMode =
DataGridViewSelectionMode.FullRowSelect;
dgFornecedor.AllowUserToDeleteRows = false;
dgFornecedor.AllowUserToAddRows = false;
dgFornecedor.ReadOnly = true;
}
catch (SqlException erro)
{
MessageBox.Show("Erro: " + erro.Message);
}
}
public static DataTable pesquisaFornecedor(string nome)
{
try
{
SqlConnection con = new SqlConnection(Dados.Conexao);
con.Open();
string sqlPesquisar =
"Select * from fornecedor where nome like @nome";
SqlDataAdapter da = new SqlDataAdapter(sqlPesquisar, con);
da.SelectCommand.Parameters.Add("@nome", SqlDbType.VarChar).Value =
"%" + nome + "%";
DataTable dt = new DataTable();
da.Fill(dt);
return dt;
con.Close();
}
catch (SqlException erro)
{
MessageBox.Show("Erro ao Pesquisar o nome do Fornecedor!"
+ erro.Message);
return null;
}
}

private void FrmFornecedores_Load(object sender, EventArgs e)
{

}
}
}
