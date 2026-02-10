# audit
ferramenta de apoio a auditoria
audit/
├── app.py
├── requirements.txt
├── README.md
├── .gitignore
└── docs/
    └── HEATMAP_FEATURE.md (opcional)
    ```python
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from fpdf import FPDF
import tempfile
import os
import datetime
import networkx as nx

# Configuração da página
st.set_page_config(page_title="Sistema de Auditoria", layout="wide")

# Estilo customizado (cinza e azul)
st.markdown("""
    <style>
        body {
            background-color: #f0f2f6;
        }
        .stApp {
            background-color: #f8f9fa;
        }
        h1, h2, h3 {
            color: #003366;
        }
        .stButton>button {
            background-color: #003366;
            color: white;
            border-radius: 8px;
        }
    </style>
""", unsafe_allow_html=True)

# Título
st.title("📊 Sistema de Auditoria Integrado")

# Seleção de tipo de auditoria
st.header("Seleção de Auditoria")
auditoria_tipo = st.selectbox(
    "Escolha o tipo de auditoria:",
    ["BACEN", "IIA", "CVM", "Receita Federal"]
)

# Cronograma
st.header("Cronograma da Auditoria")
dias = st.slider("Defina a duração (em dias):", 5, 60, 30)
st.write(f"Cronograma definido: {dias} dias")

# Escopo
st.header("Escopo da Auditoria")
escopo = st.text_area("Defina o escopo da auditoria:")
if escopo:
    st.success("Escopo registrado com sucesso!")

# Checklist
st.header("Checklist de Testes")
checklist = st.multiselect(
    "Selecione os testes:",
    ["Conformidade regulatória", "Validação de processos",
     "Segurança da informação", "Controles internos",
     "Gestão de riscos"]
)
st.write("Itens selecionados:", checklist)

# Upload de documentos de referência
st.header("Documentos de Referência")
uploaded_files = st.file_uploader("Carregue documentos (PDF, DOCX, XLSX, TXT)", 
                                  type=["pdf", "docx", "xlsx", "txt"], 
                                  accept_multiple_files=True)
documentos = []
if uploaded_files:
    for file in uploaded_files:
        documentos.append(file.name)
    st.success(f"{len(documentos)} documento(s) carregado(s): {', '.join(documentos)}")

# Simulação de resultados
st.header("Resultados da Auditoria")
dados = pd.DataFrame({
    "Área": ["Conformidade", "Processos", "Segurança", "Controles", "Riscos"],
    "Percentual": [85, 70, 90, 75, 65]
})

fig, ax = plt.subplots()
ax.bar(dados["Área"], dados["Percentual"], color="#003366")
ax.set_ylabel("Percentual de Conformidade (%)")
ax.set_title("Resultados da Auditoria")
st.pyplot(fig)

# Cronograma estilo Gantt simplificado
st.header("Cronograma (Gantt Simplificado)")
etapas = ["Planejamento", "Execução", "Testes", "Análise", "Relatório"]
inicio = datetime.date.today()
datas = [inicio + datetime.timedelta(days=i*(dias//len(etapas))) for i in range(len(etapas))]

cronograma = pd.DataFrame({"Etapa": datas})
st.table(cronograma)

# Mapeamento de Processos
st.header("Mapeamento de Processos")
processos = ["Entrada de Dados", "Validação", "Controle Interno", "Análise", "Relatório Final"]

G = nx.DiGraph()
edges = [("Entrada de Dados", "Validação"),
         ("Validação", "Controle Interno"),
         ("Controle Interno", "Análise"),
         ("Análise", "Relatório Final")]
G.add_edges_from(edges)

fig2, ax2 = plt.subplots(figsize=(6,4))
pos = nx.spring_layout(G)
nx.draw(G, pos, with_labels=True, node_color="#003366", node_size=2000,
        font_color="white", font_size=10, arrows=True, ax=ax2)
st.pyplot(fig2)

# Progresso por etapa (gráfico de linha)
st.header("Acompanhamento de Progresso")
progresso = pd.DataFrame({
    "Etapa": etapas,
    "Progresso (%)": [20, 40, 60, 80, 100]
})

fig3, ax3 = plt.subplots()
ax3.plot(progresso["Etapa"], progresso["Progresso (%)"], marker="o", color="#003366")
ax3.set_ylabel("Progresso (%)")
ax3.set_title("Evolução do Progresso por Etapa")
st.pyplot(fig3)

# Heatmap de Riscos
st.header("Indicadores de Risco (Heatmap)")
risco_data = pd.DataFrame({
    "Conformidade": [2],
    "Processos": [3],
    "Segurança": [1],
    "Controles": [4],
    "Riscos": [5]
}, index=["Nível de Risco"])

fig4, ax4 = plt.subplots(figsize=(6,2))
sns.heatmap(risco_data, annot=True, cmap="Reds", cbar=True, ax=ax4)
ax4.set_title("Mapa de Riscos")
st.pyplot(fig4)

# Resumo Executivo
st.header("Resumo Executivo")
resumo = f"""
Auditoria: {auditoria_tipo}
Escopo: {escopo if escopo else "Não definido"}
Duração: {dias} dias
Checklist: {", ".join(checklist) if checklist else "Nenhum item selecionado"}
Documentos de Referência: {", ".join(documentos) if documentos else "Nenhum documento carregado"}
"""
st.text(resumo)
```
