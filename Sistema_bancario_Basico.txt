import datetime
import random

menu = """

[d] Depositar
[s] Sacar
[e] Extrato
[i] Insights Inteligentes
[c] Currículo Financeiro
[q] Sair

=> """

# Variáveis do sistema bancário
saldo = 0
limite = 500
extrato = ""
numero_saques = 0
LIMITE_SAQUES = 3

# Sistema inteligente de categorização e currículo
transacoes = []
categorias_deposito = ['Salário', 'Freelance', 'Investimentos', 'Presente', 'Outros']
dicas_financeiras = [
    "💡 Dica: Mantenha sempre uma reserva de emergência de 3-6 meses de gastos.",
    "💡 Dica: Diversifique seus investimentos para reduzir riscos.",
    "💡 Dica: Acompanhe seus gastos mensalmente para ter controle financeiro.",
    "💡 Dica: Defina metas financeiras claras e realistas.",
    "💡 Dica: Evite dívidas desnecessárias, especialmente no cartão de crédito."
]

while True:

    opcao = input(menu)

    if opcao == "d":
        valor = float(input("Informe o valor do depósito: "))

        if valor > 0:
            print("\n🏷️  Categorize seu depósito:")
            for i, categoria in enumerate(categorias_deposito, 1):
                print(f"[{i}] {categoria}")
            
            try:
                escolha = int(input("Escolha a categoria (1-5): "))
                if 1 <= escolha <= 5:
                    categoria = categorias_deposito[escolha-1]
                else:
                    categoria = "Outros"
            except:
                categoria = "Outros"
            
            saldo += valor
            data_hora = datetime.datetime.now().strftime("%d/%m/%Y %H:%M")
            transacao = {
                'tipo': 'Depósito',
                'valor': valor,
                'categoria': categoria,
                'data': data_hora
            }
            transacoes.append(transacao)
            extrato += f"Depósito: R$ {valor:.2f} ({categoria}) - {data_hora}\n"
            
            # Feedback inteligente
            if valor >= 1000:
                print("✨ Excelente! Depósito significativo. Considere investir parte deste valor.")
            elif categoria == "Salário":
                print("💼 Ótimo! Lembre-se de separar uma parte para reserva de emergência.")

        else:
            print("Operação falhou! O valor informado é inválido.")

    elif opcao == "s":
        valor = float(input("Informe o valor do saque: "))

        excedeu_saldo = valor > saldo
        excedeu_limite = valor > limite
        excedeu_saques = numero_saques >= LIMITE_SAQUES

        if excedeu_saldo:
            print("Operação falhou! Você não tem saldo suficiente.")
            if saldo > 0:
                print(f"💡 Seu saldo atual é R$ {saldo:.2f}. Considere sacar um valor menor.")

        elif excedeu_limite:
            print("Operação falhou! O valor do saque excede o limite.")
            print(f"💡 Seu limite de saque é R$ {limite:.2f}.")

        elif excedeu_saques:
            print("Operação falhou! Número máximo de saques excedido.")
            print(f"💡 Você já realizou {LIMITE_SAQUES} saques hoje. Tente novamente amanhã.")

        elif valor > 0:
            saldo -= valor
            data_hora = datetime.datetime.now().strftime("%d/%m/%Y %H:%M")
            transacao = {
                'tipo': 'Saque',
                'valor': valor,
                'categoria': 'Saque',
                'data': data_hora
            }
            transacoes.append(transacao)
            extrato += f"Saque: R$ {valor:.2f} - {data_hora}\n"
            numero_saques += 1
            
            # Feedback inteligente
            percentual_saldo = (valor / (saldo + valor)) * 100
            if percentual_saldo > 50:
                print("⚠️  Atenção! Você sacou mais de 50% do seu saldo.")
            elif numero_saques == LIMITE_SAQUES:
                print("📊 Este foi seu último saque do dia. Planeje bem seus próximos gastos!")

        else:
            print("Operação falhou! O valor informado é inválido.")

    elif opcao == "e":
        print("\n================ EXTRATO INTELIGENTE ================")
        if not extrato:
            print("Não foram realizadas movimentações.")
        else:
            print(extrato)
            
            # Análises inteligentes
            if len(transacoes) > 0:
                total_depositos = sum(t['valor'] for t in transacoes if t['tipo'] == 'Depósito')
                total_saques = sum(t['valor'] for t in transacoes if t['tipo'] == 'Saque')
                
                print(f"\n📊 ANÁLISE INTELIGENTE:")
                print(f"💰 Total depositado: R$ {total_depositos:.2f}")
                print(f"💸 Total sacado: R$ {total_saques:.2f}")
                
                if total_depositos > 0:
                    taxa_economia = ((total_depositos - total_saques) / total_depositos) * 100
                    print(f"📈 Taxa de economia: {taxa_economia:.1f}%")
                    
                    if taxa_economia > 70:
                        print("🌟 Parabéns! Você está poupando muito bem!")
                    elif taxa_economia > 30:
                        print("👍 Boa economia! Continue assim!")
                    else:
                        print("⚠️  Atenção: considere reduzir seus gastos.")
        
        print(f"\n💰 Saldo atual: R$ {saldo:.2f}")
        print("==================================================")

    elif opcao == "i":
        print("\n🧠 ================ INSIGHTS INTELIGENTES ================")
        
        if len(transacoes) == 0:
            print("💭 Ainda não há dados suficientes para análises.")
            print("💡 Realize algumas transações para ver insights personalizados!")
        else:
            # Análise de categorias
            categorias_count = {}
            for transacao in transacoes:
                if transacao['tipo'] == 'Depósito':
                    cat = transacao['categoria']
                    categorias_count[cat] = categorias_count.get(cat, 0) + transacao['valor']
            
            if categorias_count:
                print("📈 ORIGEM DOS SEUS DEPÓSITOS:")
                for categoria, valor in categorias_count.items():
                    print(f"   {categoria}: R$ {valor:.2f}")
                
                principal_fonte = max(categorias_count, key=categorias_count.get)
                print(f"\n💼 Sua principal fonte de renda: {principal_fonte}")
            
            # Análise de comportamento
            if numero_saques > 0:
                print(f"\n📊 ANÁLISE DE COMPORTAMENTO:")
                print(f"   Saques realizados hoje: {numero_saques}/{LIMITE_SAQUES}")
                
                if numero_saques == LIMITE_SAQUES:
                    print("   🔴 Limite de saques atingido - Planeje melhor!")
                elif numero_saques >= 2:
                    print("   🟡 Alto número de saques - Considere planejamento!")
                else:
                    print("   🟢 Uso consciente dos saques!")
            
            # Dica aleatória
            print(f"\n{random.choice(dicas_financeiras)}")
        
        print("======================================================")

    elif opcao == "c":
        print("\n📚 ================ CURRÍCULO FINANCEIRO ================")
        print("🎓 LIÇÕES DE EDUCAÇÃO FINANCEIRA")
        print("\n📖 Lição 1: Controle de Gastos")
        print("   • Sempre acompanhe seus gastos mensais")
        print("   • Use categorias para organizar suas despesas")
        print("   • Evite gastos impulsivos")
        
        print("\n📖 Lição 2: Reserva de Emergência")
        print("   • Mantenha 3-6 meses de gastos guardados")
        print("   • Use apenas em verdadeiras emergências")
        print("   • Priorize formar esta reserva antes de investir")
        
        print("\n📖 Lição 3: Planejamento Financeiro")
        print("   • Defina metas financeiras claras")
        print("   • Crie um orçamento mensal")
        print("   • Revise suas metas regularmente")
        
        print("\n📖 Lição 4: Investimentos Básicos")
        print("   • Diversifique seus investimentos")
        print("   • Comece com produtos de baixo risco")
        print("   • Estude antes de investir")
        
        print("\n🎯 DESAFIO PESSOAL:")
        if saldo < 100:
            print("   📌 Meta: Acumular R$ 100,00 de reserva")
        elif saldo < 500:
            print("   📌 Meta: Atingir R$ 500,00 de reserva")
        else:
            print("   📌 Meta: Manter o bom trabalho e considerar investimentos!")
        
        print(f"\n💰 Seu saldo atual: R$ {saldo:.2f}")
        print("=========================================================")

    elif opcao == "q":
        print("\n👋 Obrigado por usar o Sistema Bancário Inteligente!")
        print("💡 Lembre-se: educação financeira é um investimento em você!")
        break

    else:
        print("Operação inválida, por favor selecione novamente a operação desejada.")