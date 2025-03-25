import json
import streamlit as st

DATA_FILE = "salas.json"
SALAS = ["VM 1", "VM 2", "VM", "VM 4", "VM Antiga"]

def carregar_dados():
    try:
        with open(DATA_FILE, "r") as file:
            return json.load(file)
    except (FileNotFoundError, json.JSONDecodeError):
        return {}

def salvar_dados(dados):
    with open(DATA_FILE, "w") as file:
        json.dump(dados, file, indent=4)

def ocupar_vm(usuario, sala):
    dados = carregar_dados()
    if sala in dados:
        st.error(f"A VM {sala} já está sendo usada por {dados[sala]}.")
    else:
        dados[sala] = usuario
        salvar_dados(dados)
        atualizar_lista()
        st.success(f"{usuario} ocupou a VM {sala}.")

def liberar_vm(sala):
    dados = carregar_dados()
    if sala in dados:
        del dados[sala]
        salvar_dados(dados)
        atualizar_lista()
        st.success(f"A VM {sala} foi liberada.")
    else:
        st.warning(f"A VM {sala} já está livre.")

def atualizar_lista():
    dados = carregar_dados()
    for sala in SALAS:
        status = f"{sala}: {dados[sala]}" if sala in dados else f"{sala}: Livre"
        st.text(status)

# Interface com Streamlit
st.title("Controle de VMs")

usuario = st.text_input("Digite seu nome:")
sala = st.selectbox("Escolha a VM", SALAS)

if st.button("Ocupar"):
    if usuario:
        ocupar_vm(usuario, sala)
    else:
        st.warning("Digite seu nome!")

if st.button("Liberar"):
    liberar_vm(sala)

st.subheader("Status das Salas")
atualizar_lista()
