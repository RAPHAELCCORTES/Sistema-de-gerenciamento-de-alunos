# Sistema-de-gerenciamento-de-alunos
Criação de um sistema de gerenciamento de alunos, baseado em uma atividade acadêmica da programação de computadores.


#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    int codigo;
    char nome[50];
    char estado[30];
    int populacao;
} Cidade;

void incluirCidade(FILE *arquivo) {
    Cidade cidade;

    printf("Digite o código da cidade: ");
    scanf("%d", &cidade.codigo);

    printf("Digite o nome da cidade: ");
    getchar(); // Limpa o buffer
    fgets(cidade.nome, sizeof(cidade.nome), stdin);
    cidade.nome[strcspn(cidade.nome, "\n")] = '\0'; // Remove o '\n'

    printf("Digite o estado da cidade: ");
    fgets(cidade.estado, sizeof(cidade.estado), stdin);
    cidade.estado[strcspn(cidade.estado, "\n")] = '\0';

    printf("Digite a população da cidade: ");
    scanf("%d", &cidade.populacao);

    fprintf(arquivo, "%d;%s;%s;%d\n", cidade.codigo, cidade.nome, cidade.estado, cidade.populacao);
    printf("Cidade adicionada com sucesso!\n");
}

void alterarCidade() {
    FILE *arquivo = fopen("cidades.txt", "r");
    FILE *temp = fopen("temp.txt", "w");
    Cidade cidade;
    int codigo, encontrado = 0;

    if (!arquivo || !temp) {
        printf("Erro ao abrir o arquivo!\n");
        return;
    }

    printf("Digite o código da cidade a ser alterada: ");
    scanf("%d", &codigo);

    while (fscanf(arquivo, "%d;%49[^;];%29[^;];%d\n", &cidade.codigo, cidade.nome, cidade.estado, &cidade.populacao) != EOF) {
        if (cidade.codigo == codigo) {
            encontrado = 1;

            printf("Digite o novo nome da cidade (atual: %s): ", cidade.nome);
            getchar(); // Limpa o buffer
            fgets(cidade.nome, sizeof(cidade.nome), stdin);
            cidade.nome[strcspn(cidade.nome, "\n")] = '\0';

            printf("Digite o novo estado (atual: %s): ", cidade.estado);
            fgets(cidade.estado, sizeof(cidade.estado), stdin);
            cidade.estado[strcspn(cidade.estado, "\n")] = '\0';

            printf("Digite a nova população (atual: %d): ", cidade.populacao);
            scanf("%d", &cidade.populacao);
        }
        fprintf(temp, "%d;%s;%s;%d\n", cidade.codigo, cidade.nome, cidade.estado, cidade.populacao);
    }

    fclose(arquivo);
    fclose(temp);

    remove("cidades.txt");
    rename("temp.txt", "cidades.txt");

    if (encontrado) {
        printf("Dados da cidade atualizados com sucesso!\n");
    } else {
        printf("Cidade não encontrada!\n");
    }
}

void excluirCidade() {
    FILE *arquivo = fopen("cidades.txt", "r");
    FILE *temp = fopen("temp.txt", "w");
    Cidade cidade;
    int codigo, encontrado = 0;

    if (!arquivo || !temp) {
        printf("Erro ao abrir o arquivo!\n");
        return;
    }

    printf("Digite o código da cidade a ser excluída: ");
    scanf("%d", &codigo);

    while (fscanf(arquivo, "%d;%49[^;];%29[^;];%d\n", &cidade.codigo, cidade.nome, cidade.estado, &cidade.populacao) != EOF) {
        if (cidade.codigo == codigo) {
            encontrado = 1;
            printf("Cidade excluída com sucesso!\n");
        } else {
            fprintf(temp, "%d;%s;%s;%d\n", cidade.codigo, cidade.nome, cidade.estado, cidade.populacao);
        }
    }

    fclose(arquivo);
    fclose(temp);

    remove("cidades.txt");
    rename("temp.txt", "cidades.txt");

    if (!encontrado) {
        printf("Cidade não encontrada!\n");
    }
}

void consultarCidade() {
    FILE *arquivo = fopen("cidades.txt", "r");
    Cidade cidade;
    int codigo, encontrado = 0;

    if (!arquivo) {
        printf("Erro ao abrir o arquivo!\n");
        return;
    }

    printf("Digite o código da cidade: ");
    scanf("%d", &codigo);

    while (fscanf(arquivo, "%d;%49[^;];%29[^;];%d\n", &cidade.codigo, cidade.nome, cidade.estado, &cidade.populacao) != EOF) {
        if (cidade.codigo == codigo) {
            encontrado = 1;
            printf("Código: %d\n", cidade.codigo);
            printf("Nome: %s\n", cidade.nome);
            printf("Estado: %s\n", cidade.estado);
            printf("População: %d\n", cidade.populacao);
            break;
        }
    }

    fclose(arquivo);

    if (!encontrado) {
        printf("Cidade não encontrada!\n");
    }
}

void listarCidades() {
    FILE *arquivo = fopen("cidades.txt", "r");
    Cidade cidade;

    if (!arquivo) {
        printf("Erro ao abrir o arquivo!\n");
        return;
    }

    printf("Lista de cidades cadastradas:\n");
    printf("-----------------------------------\n");

    while (fscanf(arquivo, "%d;%49[^;];%29[^;];%d\n", &cidade.codigo, cidade.nome, cidade.estado, &cidade.populacao) != EOF) {
        printf("Código: %d\n", cidade.codigo);
        printf("Nome: %s\n", cidade.nome);
        printf("Estado: %s\n", cidade.estado);
        printf("População: %d\n", cidade.populacao);
        printf("-----------------------------------\n");
    }

    fclose(arquivo);
}

int main() {
    FILE *arquivo;
    int opcao;

    do {
        printf("\n--- Sistema de Cadastro de Cidades ---\n");
        printf("1. Incluir cidade\n");
        printf("2. Alterar dados de uma cidade\n");
        printf("3. Excluir cidade\n");
        printf("4. Consultar cidade\n");
        printf("5. Listar cidades\n");
        printf("6. Sair\n");
        printf("Escolha uma opção: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                arquivo = fopen("cidades.txt", "a");
                if (arquivo) {
                    incluirCidade(arquivo);
                    fclose(arquivo);
                } else {
                    printf("Erro ao abrir o arquivo!\n");
                }
                break;
            case 2:
                alterarCidade();
                break;
            case 3:
                excluirCidade();
                break;
            case 4:
                consultarCidade();
                break;
            case 5:
                listarCidades();
                break;
            case 6:
                printf("Encerrando o programa...\n");
                break;
            default:
                printf("Opção inválida!\n");
        }
    } while (opcao != 6);

    return 0;
}
