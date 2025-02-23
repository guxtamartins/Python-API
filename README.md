from flask import Flask, jsonify, render_template, request
import os

app = Flask(__name__)

# Dados dos clientes (simulando um banco de dados)
clientes = [
    {"id": 1, "nome": "Cliente A", "email": "clientea@email.com"},
    {"id": 2, "nome": "Cliente B", "email": "clienteb@email.com"}
]

# Listar todos os clientes
@app.route("/api/clientes", methods=["GET"])
def listar_clientes():
    return jsonify(clientes)

# Adicionar um novo cliente
@app.route("/api/clientes", methods=["POST"])
def adicionar_cliente():
    novo_cliente = request.json
    if not novo_cliente or "nome" not in novo_cliente or "email" not in novo_cliente:
        return jsonify({"erro": "Dados inválidos"}), 400
    novo_cliente["id"] = max([c["id"] for c in clientes], default=0) + 1
    clientes.append(novo_cliente)
    return jsonify(novo_cliente), 201

# Editar um cliente existente
@app.route("/api/clientes/<int:id>", methods=["PUT"])
def editar_cliente(id):
    for cliente in clientes:
        if cliente["id"] == id:
            cliente.update(request.json)
            return jsonify(cliente)
    return jsonify({"erro": "Cliente não encontrado"}), 404

# Excluir um cliente
@app.route("/api/clientes/<int:id>", methods=["DELETE"])
def excluir_cliente(id):
    global clientes
    clientes = [cliente for cliente in clientes if cliente["id"] != id]
    return jsonify({"mensagem": "Cliente excluído com sucesso"})

# Página principal
@app.route("/")
def home():
    return render_template("index.html", clientes=clientes)

if __name__ == "__main__":
    try:
        port = int(os.environ.get("PORT", 5000))
        app.run(host='0.0.0.0', port=port, debug=False)
    except OSError as e:
        print(f"Erro ao iniciar o servidor. Verifique se a porta {port} está disponível. Erro: {e}")
    except Exception as e:
        print(f"Erro inesperado ao iniciar o servidor: {e}")
