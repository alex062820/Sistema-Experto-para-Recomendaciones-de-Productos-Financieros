# Sistema-Experto-para-Recomendaciones-de-Productos-Financieros
Este proyecto implementa un sistema experto con CLIPS que recomienda productos financieros basados en las preferencias del usuario, como tipo de producto (inversión, crédito, ahorro), nivel de riesgo o plazo. Usa reglas predefinidas para optimizar la toma de decisiones en banca y finanzas.

import clips

# Creamos el entorno CLIPS
environment = clips.Environment()

class Regla:
    def __init__(self, condiciones, conclusion):
        self.condiciones = condiciones
        self.conclusion = conclusion

def definir_hechos_y_reglas(hechos, reglas):
    environment.clear()
    
    # Insertamos hechos en el entorno CLIPS
    for hecho in hechos:
        environment.assert_string(f"(hecho {hecho})")

    # Definimos reglas en CLIPS
    for idx, regla in enumerate(reglas):
        condiciones = " ".join([f"(hecho {cond})" for cond in regla.condiciones])
        environment.build(f"""
        (defrule regla-{idx + 1}
            {condiciones}
            =>
            (assert (conclusion "{regla.conclusion}")) )
        """)

def obtener_conclusiones():
    conclusiones = set()
    
    environment.run()
    
    # Recolectamos las conclusiones obtenidas
    for fact in environment.facts():
        fact_str = str(fact)
        if fact_str.startswith("(conclusion "):
            conclusion = fact_str.split('"')[1]
            conclusiones.add(conclusion)
    
    return conclusiones

def main():
    # Definimos las reglas para sugerencias financieras
    reglas = [
        Regla({"inversion", "riesgo alto"}, "Fondo de inversión en acciones"),
        Regla({"inversion", "riesgo bajo"}, "Certificado de depósito a plazo fijo"),
        Regla({"credito", "plazo corto"}, "Crédito personal"),
        Regla({"credito", "plazo largo"}, "Préstamo hipotecario"),
        Regla({"ahorro", "corto plazo"}, "Cuenta de ahorro"),
        Regla({"ahorro", "largo plazo"}, "Fondo de pensiones")
    ]

    print("¡Bienvenido al Sistema Experto de Productos Financieros!\n")
    conclusiones_acumuladas = set()

    while True:
        print("Por favor, responde las siguientes preguntas sobre tus necesidades financieras:")
        
        # Pregunta sobre el tipo de producto financiero
        print("\nTipos de productos disponibles:")
        print("- Inversión")
        print("- Crédito")
        print("- Ahorro")
        tipo_producto = input("¿Qué tipo de producto financiero necesitas? ").strip().lower()

        tipos_disponibles = {"inversion", "credito", "ahorro"}
        if tipo_producto not in tipos_disponibles:
            print("Tipo de producto no reconocido. Por favor, elige uno de los tipos listados.")
            continue

        # Pregunta sobre el perfil de riesgo o plazo
        if tipo_producto == "inversion":
            print("\nNivel de riesgo disponible:")
            print("- Alto")
            print("- Bajo")
            riesgo = input("¿Qué nivel de riesgo estás dispuesto a asumir? ").strip().lower()

            riesgos_disponibles = {"alto", "bajo"}
            if riesgo not in riesgos_disponibles:
                print("Riesgo no reconocido. Por favor, elige 'alto' o 'bajo'.")
                continue
            
            hechos = {tipo_producto, f"riesgo {riesgo}"}
        
        elif tipo_producto == "credito" or tipo_producto == "ahorro":
            print("\nPlazos disponibles:")
            print("- Corto plazo")
            print("- Largo plazo")
            plazo = input("¿Prefieres un producto a corto o largo plazo? ").strip().lower()

            plazos_disponibles = {"corto plazo", "largo plazo"}
            if plazo not in plazos_disponibles:
                print("Plazo no reconocido. Por favor, elige 'corto plazo' o 'largo plazo'.")
                continue

            hechos = {tipo_producto, plazo}
        
        # Definimos los hechos y reglas
        definir_hechos_y_reglas(hechos, reglas)

        # Obtenemos las conclusiones del sistema
        recomendaciones = obtener_conclusiones()
        conclusiones_acumuladas.update(recomendaciones)

        if recomendaciones:
            print("\nCon base en tus respuestas, te recomendamos el siguiente producto financiero: ")
            for producto in recomendaciones:
                print(f"- {producto}")
        else:
            print("\nLo siento, no se encontró una recomendación basada en tus preferencias.")

        # Pregunta si quiere continuar con otra recomendación
        while True:
            respuesta = input("\n¿Deseas realizar otra recomendación? (s/n): ").strip().lower()
            if respuesta == "s":
                break
            elif respuesta == "n":
                print("\nGracias por usar el Sistema Experto de Productos Financieros.")
                if conclusiones_acumuladas:
                    print("\nConclusiones acumuladas durante la sesión:")
                    for producto in conclusiones_acumuladas:
                        print(f"- {producto}")
                else:
                    print("\nNo se derivaron recomendaciones durante la sesión.")
                return
            else:
                print("Respuesta no válida. Por favor, ingresa 's' para sí o 'n' para no.")

if __name__ == "__main__":
    main()
