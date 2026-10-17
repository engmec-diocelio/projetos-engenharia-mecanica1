# projetos-engenharia-mecanica1
Projetos acadêmicos e simulações desenvolvidas em Engenharia Mecânica (UNIFOR).
"""
===============================================================================
PROJETO: ECOPATAS SMART - MÓDULO DE CÁLCULO TÉCNICO E ENGENHARIA
Autor: Diocélio Filho (UNIFOR)
Descrição: Scripts de dimensionamento estrutural, elétrico e análise ESG
           com base nas normas ABNT, ASTM e ISO citadas no artigo.
===============================================================================
"""

import math

class EcoPatasMecanica:
    """
    Módulo de Análise Mecânica e Estrutural dos Compósitos Reciclados
    e Estrutura do Mobiliário Urbano.
    """
    def __init__(self, fck_concreto=25.0):
        # fck do concreto em MPa (NBR 6118)
        self.fck = fck_concreto
        # Dados de ensaios do compósito reciclado (ISO 14126 e ASTM D7078)
        self.resistencia_compressao_media_mpa = 219.75
        self.modulo_cisalhamento_gba = 3.84

    def verificar_resistencia_cobertura(self, carga_aplicada_kpa):
        """
        Verifica a margem de segurança da cobertura de fibra de vidro/epóxi.
        """
        resistencia_kpa = self.resistencia_compressao_media_mpa * 1000
        fator_seguranca = resistencia_kpa / carga_aplicada_kpa
        
        return {
            "resistencia_media_mpa": self.resistencia_compressao_media_mpa,
            "carga_aplicada_kpa": carga_aplicada_kpa,
            "fator_seguranca": round(fator_seguranca, 2),
            "status": "Aprovado" if fator_seguranca >= 2.0 else "Reprovado"
        }

    def calcular_massa_estrutura_concreto(self, diametro_base_m=2.0, espessura_parede_m=0.08, altura_m=0.48):
        """
        Calcula o volume e a massa do banco/abrigo em concreto armado.
        Massa específica do concreto armado: 2500 kg/m³ (NBR 6118).
        """
        r_ext = diametro_base_m / 2.0
        r_int = r_ext - espessura_parede_m
        
        area_secao = math.pi * (r_ext**2 - r_int**2)
        volume_m3 = area_secao * altura_m
        massa_kg = volume_m3 * 2500.0
        
        return {
            "volume_concreto_m3": round(volume_m3, 3),
            "massa_total_kg": round(massa_kg, 2)
        }


class EcoPatasEletrico:
    """
    Módulo de Dimensionamento Elétrico Off-Grid Fotovoltaico.
    """
    def __init__(self, irradiacao_diaria_kwh_m2=5.8):
        # Irradiação solar média de Fortaleza/CE (5.8 kWh/m²/dia)
        self.irradiacao = irradiacao_diaria_kwh_m2

    def dimensionar_sistema_solar(self, consumo_diario_wh=130.0, potencia_painel_wp=100.0, rendimento_sistema=0.75):
        """
        Calcula a geração diária e a autonomia energética.
        """
        hsp = self.irradiacao  # Horas de Sol Pleno
        geracao_diaria_wh = potencia_painel_wp * hsp * rendimento_sistema
        fator_autonomia = geracao_diaria_wh / consumo_diario_wh
        
        return {
            "geracao_diaria_estimada_wh": round(geracao_diaria_wh, 2),
            "consumo_diario_estimado_wh": consumo_diario_wh,
            "balanco_energetico": "Superavitário" if geracao_diaria_wh > consumo_diario_wh else "Deficitário",
            "razao_geracao_consumo": round(fator_autonomia, 2)
        }

    def verificar_autonomia_bateria(self, capacidade_ah=100.0, tensao_v=12.0, dod_maximo=0.5, consumo_diario_wh=130.0):
        """
        Calcula a autonomia em horas considerando limite de descarga segura (DoD = 50%).
        """
        energia_total_wh = capacidade_ah * tensao_v
        energia_utilizavel_wh = energia_total_wh * dod_maximo
        
        dias_autonomia = energia_utilizavel_wh / consumo_diario_wh
        horas_autonomia = dias_autonomia * 24.0
        
        return {
            "capacidade_bateria_wh": energia_total_wh,
            "energia_utilizavel_wh": energia_utilizavel_wh,
            "autonomia_horas": round(horas_autonomia, 1),
            "atende_requisito_72h": horas_autonomia >= 72.0
        }


class EcoPatasESG:
    """
    Módulo de Métricas e Impacto Ambiental / Economia Circular.
    """
    @staticmethod
    def calcular_impacto_upcycling(qtd_pas_processadas, peso_medio_pa_ton=12.0):
        """
        Calcula a quantidade de compósito desviado de aterros sanitários e CO2 evitado.
        """
        massa_total_ton = qtd_pas_processadas * peso_medio_pa_ton
        fibra_vidro_ton = massa_total_ton * 0.70
        resina_epoxi_ton = massa_total_ton * 0.30
        co2_evitado_ton = massa_total_ton * 1.8
        
        return {
            "pas_processadas": qtd_pas_processadas,
            "massa_composito_recuperado_ton": massa_total_ton,
            "fibra_vidro_recuperada_ton": round(fibra_vidro_ton, 2),
            "resina_epoxi_recuperada_ton": round(resina_epoxi_ton, 2),
            "estimativa_co2_evitado_ton": round(co2_evitado_ton, 2)
        }


if __name__ == "__main__":
    print("=" * 60)
    print("      SIMULAÇÃO TÉCNICA - PROJETO ECOPATAS SMART (UNIFOR)")
    print("=" * 60)
    
    # 1. Análise Mecânica
    mec = EcoPatasMecanica()
    res_mec = mec.verificar_resistencia_cobertura(carga_aplicada_kpa=15.0)
    massa_est = mec.calcular_massa_estrutura_concreto()
    print("\n[1] ANÁLISE ESTRUTURAL E MECÂNICA:")
    print(f" - Resistência Média à Compressão: {res_mec['resistencia_media_mpa']} MPa")
    print(f" - Fator de Segurança Calculado: {res_mec['fator_seguranca']}")
    print(f" - Massa Total Estimada da Base: {massa_est['massa_total_kg']} kg")

    # 2. Análise Elétrica
    eletro = EcoPatasEletrico()
    res_ele = eletro.dimensionar_sistema_solar()
    res_bat = eletro.verificar_autonomia_bateria()
    print("\n[2] SISTEMA FOTOVOLTAICO OFF-GRID:")
    print(f" - Geração Diária Estimada: {res_ele['geracao_diaria_estimada_wh']} Wh/dia")
    print(f" - Consumo Diário Estimado: {res_ele['consumo_diario_estimado_wh']} Wh/dia")
    print(f" - Status do Balanço Energético: {res_ele['balanco_energetico']}")
    print(f" - Autonomia Estimada da Bateria: {res_bat['autonomia_horas']} horas")

    # 3. Análise ESG
    res_esg = EcoPatasESG.calcular_impacto_upcycling(qtd_pas_processadas=5)
    print("\n[3] IMPACTO AMBIENTAL E ECONOMIA CIRCULAR:")
    print(f" - Compósito Total Recuperado (5 pás): {res_esg['massa_composito_recuperado_ton']} toneladas")
    print(f" - Estimativa de CO2 Evitado: {res_esg['estimativa_co2_evitado_ton']} toneladas")
    print("=" * 60)
    
