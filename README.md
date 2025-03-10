# ProjetoForms1.FrmFornecedores
Inclusão e validação de Fornecedores

using System;
using System.Collections.Generic;
using System.Windows.Forms;
using System.Drawing;

namespace ProjetoForms1
{
    public partial class FrmFornecedores : Form
    {
        private List<Fornecedor> fornecedores = new List<Fornecedor>();
        private DataGridView dgvFornecedores;
        private Button btnAdicionar;
        private Button btnAtualizar;
        private Button btnExcluir;
        private FornecedorController fornecedorController;

  public FrmFornecedores()
        {
            InitializeComponent();
            fornecedorController = new FornecedorController(); // Inicializa o controlador
            ConfigurarDataGridView();
            ConfigurarBotoes(GetBtnExcluir());
            AtualizarGrid();
        }

  private void ConfigurarDataGridView()
        {
            // Inicializa o DataGridView
            dgvFornecedores = new DataGridView
            {
                Location = new Point(10, 10),
                Size = new Size(780, 300),
                AutoGenerateColumns = false,
                SelectionMode = DataGridViewSelectionMode.FullRowSelect,
                MultiSelect = false,
                ReadOnly = true
            };

  // Adiciona colunas manualmente
            dgvFornecedores.Columns.Add("Id", "Id");
            dgvFornecedores.Columns.Add("Nome", "Nome");
            dgvFornecedores.Columns.Add("CNPJ", "CNPJ");
            dgvFornecedores.Columns.Add("Telefone", "Telefone");
            dgvFornecedores.Columns.Add("Email", "Email");
            dgvFornecedores.Columns.Add("Endereco", "Endereco");
            dgvFornecedores.Columns.Add("Cidade", "Cidade");
            dgvFornecedores.Columns.Add("Estado", "Estado");
            dgvFornecedores.Columns.Add("CEP", "CEP");
            dgvFornecedores.Columns.Add("Observacoes", "Observacoes");

  // Adiciona o DataGridView ao formulário
            Controls.Add(dgvFornecedores);
        }

  private Button GetBtnExcluir()
        {
            return btnExcluir;
        }

  private void ConfigurarBotoes(Button btnExcluir)
        {
            btnAdicionar = new Button
            {
                Location = new Point(10, 320),
                Size = new Size(100, 30),
                Text = "Adicionar"
            };
            btnAdicionar.Click += btnAdicionar_Click;
            Controls.Add(btnAdicionar);

  btnAtualizar = new Button
            {
                Location = new Point(120, 320),
                Size = new Size(100, 30),
                Text = "Atualizar"
            };

  btnExcluir = new Button
            {
                Location = new Point(230, 320),
                Size = new Size(100, 30),
                Text = "Excluir"
            };
            btnExcluir.Click += btnExcluir_Click;
            Controls.Add(btnExcluir);
        }

  private void btnExcluir_Click(object sender, EventArgs e)
        {
            throw new NotImplementedException();
        }

  private void AtualizarGrid()
        {
            // Atualiza o DataGridView com a lista de fornecedores
            dgvFornecedores.DataSource = null; // Limpa o DataSource
            dgvFornecedores.DataSource = fornecedorController.Listar();
        }

  private void btnAdicionar_Click(object sender, EventArgs e)
        {
            var fornecedor = new Fornecedor
            {
                Nome = "Novo Fornecedor",
                CNPJ = "00.000.000/0000-00",
                Telefone = "(00) 0000-0000",
                Email = "novo@fornecedor.com",
                Endereco = "Endereço",
                Cidade = "Cidade",
                Estado = "Estado",
                CEP = "00000-000",
                Observacoes = "Observações"
            };

  fornecedorController.Adicionar(fornecedor);
            AtualizarGrid();
            MessageBox.Show("Fornecedor adicionado com sucesso!");
        }

  private bool GetV()
        {
            return dgvFornecedores.SelectedRows[0].DataBoundItem is Fornecedor fornecedor;
        }

  private void btnAtualizar_Click(object sender, EventArgs e, Fornecedor fornecedor, bool v)
        {
            if (dgvFornecedores.SelectedRows.Count > 0)
            {
                if (v)
                {
                    fornecedor.Nome = "Fornecedor Atualizado";
                    fornecedorController.Atualizar(fornecedor);
                    AtualizarGrid();
                    MessageBox.Show("Fornecedor atualizado com sucesso!");
                }
            }
        }

  private Fornecedor GetFornecedor()
        {
            return dgvFornecedores.SelectedRows[0].DataBoundItem as Fornecedor;
        }

  private void btnExcluir_Click(object sender, EventArgs e, Fornecedor fornecedor)
        {
                {
                    fornecedorController.Excluir(fornecedor.Id);
                    AtualizarGrid();
                    MessageBox.Show("Fornecedor excluído com sucesso!");
                }
            }
        }
    }
