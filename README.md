#include <iostream>
#include <vector>
#include <map>
#include <string>

using namespace std;

// ----- ESTRUCTURA DE ÁRBOL -----
struct NodoCiudad {
    string nombre;
    vector<NodoCiudad*> hijos;

    NodoCiudad(string nombre) : nombre(nombre) {}

    void imprimirJerarquia(int nivel = 0) {
        for (int i = 0; i < nivel; i++) cout << "  ";
        cout << nombre << endl;
        for (auto hijo : hijos) {
            hijo->imprimirJerarquia(nivel + 1);
        }
    }
};

// ----- FUNCIÓN PARA LIBERAR MEMORIA DEL ÁRBOL -----
void liberarMemoria(NodoCiudad* nodo) {
    for (auto hijo : nodo->hijos) {
        liberarMemoria(hijo);
    }
    delete nodo;
}

// ----- GRAFO Y DISTANCIAS ENTRE CIUDADES -----
class RedCarreteras {
public:
    map<string, vector<string>> conexiones;                  // Grafo
    map<pair<string, string>, int> distancias;              // Mapa con distancias

    void agregarConexion(string ciudad1, string ciudad2, int distancia) {
        conexiones[ciudad1].push_back(ciudad2);
        conexiones[ciudad2].push_back(ciudad1); // grafo no dirigido
        distancias[{ciudad1, ciudad2}] = distancia;
        distancias[{ciudad2, ciudad1}] = distancia;
    }

    void mostrarConexiones() {
        for (auto& par : conexiones) {
            cout << par.first << " está conectada con:\n";
            for (auto& vecina : par.second) {
                cout << "  → " << vecina
                     << " (" << distancias[{par.first, vecina}] << " km)\n";
            }
        }
    }
};

// ----- PROGRAMA PRINCIPAL -----
int main() {
    // 1. Árbol de jerarquía geográfica
    NodoCiudad* pais = new NodoCiudad("Colombia");

    NodoCiudad* estado1 = new NodoCiudad("Antioquia");
    NodoCiudad* estado2 = new NodoCiudad("Cundinamarca");

    NodoCiudad* ciudadA = new NodoCiudad("Medellín");
    NodoCiudad* ciudadB = new NodoCiudad("Envigado");
    NodoCiudad* ciudadC = new NodoCiudad("Bogotá");
    NodoCiudad* ciudadD = new NodoCiudad("Zipaquirá");
    NodoCiudad* ciudadE = new NodoCiudad("Soacha");

    estado1->hijos.push_back(ciudadA);
    estado1->hijos.push_back(ciudadB);
    estado2->hijos.push_back(ciudadC);
    estado2->hijos.push_back(ciudadD);
    estado2->hijos.push_back(ciudadE);

    pais->hijos.push_back(estado1);
    pais->hijos.push_back(estado2);

    cout << "=== Jerarquía geográfica (Árbol) ===\n";
    pais->imprimirJerarquia();

    // 2. Grafo y mapa de distancias
    RedCarreteras red;

    red.agregarConexion("Medellín", "Envigado", 10);
    red.agregarConexion("Medellín", "Bogotá", 400);
    red.agregarConexion("Bogotá", "Zipaquirá", 50);
    red.agregarConexion("Bogotá", "Soacha", 20);
    red.agregarConexion("Envigado", "Soacha", 420);

    cout << "\n=== Conexiones entre ciudades (Grafo + Mapa de distancias) ===\n";
    red.mostrarConexiones();

    // 3. Liberar memoria del árbol
    liberarMemoria(pais);

    return 0;
}
