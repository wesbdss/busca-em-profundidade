# busca-em-profundidade
#include<stdio.h>
#include<stdlib.h>
#include<locale.h>

typedef struct{
	int inicio,final,cor;
}vertice;//esta parte faz um vertice

int contador=0;//esta parte o contador da descoberta e da finalização
 /*
  * O ARQUIVO DEVE SER ESCRITO DA SEGUINTE FORMA:
  * x= 
  * y= 
  * deve ser colocado dessa forma.
  * LEMBRANDO QUE A MATRIZ É APENAS DECORATIVA
  */

void busca(int **mat,vertice *ponto,int maior,int* row);//esta parte faz a busca em largura
void fila(vertice *ponto,int* row,int,int,int);//esta parte faz organiza a fila
void imprime(int **mat, vertice *ponto, int maior,int* row);//esta parte faz, imprime


int main(){
	setlocale(LC_ALL,"portuguese");
	
	FILE *arq,*arq1;//esta parte cria um ponteiro tipo arquivo
	int i=0,j,maior=0,aresta[10],x,y,s,*row;//esta parte variáveis usuais ,como a propria fila
	char arestachar[10];//esta parte é usada para converter em inteiro
	vertice *ponto;//esta parte ponteiro tipo vertice
	
	arq=fopen("arestas.txt","r+");//esta parte abre o arquivo de texto
	if(!arq){//esta parte códico quando houver erro na abertura do arquivo, e o usuário será removido
		printf("Erro na leitura, Insira o cartucho novamente!\n");
		exit(1);
	}
	printf("* O ARQUIVO DEVE SER ESCRITO DA SEGUINTE FORMA:\n* x= \n* y= \n* deve ser colocado dessa forma.\n \n");
	//rodar o arquivo pra saber o tamanha da matriz
	while(!feof(arq)){//esta parte usado para alocar o tamanho maximo da matriz.
			
		fgets(arestachar,10,arq);
		if(arestachar[0]=='x')
			x=atoi((arestachar+2));
		if(arestachar[0]=='y')
			y=atoi((arestachar+2));
		if(x > maior){
			maior = x;
		}
		if(y > maior){
			maior = y;
		}
	}
	maior = maior+1;//esta parte adiciona mais 1 pelo fato da memoria alocada começar contando do 0 , entao fica uma  posição a menos causando erro no arquivo
	
	int **mat = (int**)malloc((maior) * sizeof(int*)); //Aloca um Vetor de Ponteiros
 	 for (i=0; i < maior; i++){ //Percorre as linhas do Vetor de Ponteiros
       mat[i] = (int*) malloc(maior * sizeof(int)); 
	}
	for(i=0;i<maior;i++){//esta parte zera toda a matriz
		for(j=0;j<maior;j++){
			mat[i][j]=0;
		}
	}
	//Alocando os vertices aqui pois precisa do maior
	
	ponto = (vertice*) malloc(maior * sizeof(vertice));//esta parte aloca memoria para ponto(vertices) e row (fila).
	row = (int*)malloc(maior*sizeof(vertice));
	for(i=0;i<maior;i++){
		row[i]=-1;
	}
	for(i=0;i<maior;i++){//esta parte Inicializa todos os pontos(vertices)
		ponto[i].cor=1;
		ponto[i].inicio= -1;
		ponto[i].final= -1;
	}
	
	fclose(arq);
	//esta parte foi um teste de erro de arquivo
	arq1=fopen("arestas.txt","r");
	
	while(!feof(arq1)){	
		fgets(arestachar,10,arq1);
		if(arestachar[0]=='x'){
			x=atoi((arestachar+2));//esta parte pula 2 posições para onde se localiza os numeros e converte para inteiro
			continue;
		}
		if(arestachar[0]=='y')
			y=atoi((arestachar+2));
		mat[x][y]=1; //monta uma matriz bolada que utiliza o arquivo
		
	}

	imprime(mat,ponto,maior,row);//esta parte Imprime a patriz inicial
	
	printf("\nTemos %d vertices registrados\nEscolha o ponto S(Escolha um dos valores de \"X\" contidos no arquivo) : ",maior);
		scanf("%d",&s);//esta parte escolher o ponto inicial
		for(i=0;i<maior;i++){//esta parte procura na matriz se é válido
			if(mat[i][s] != 0){
				break;
			}
			else{
				continue;
			}
			printf("Não tem essa opção\n");
			return;
		}
		ponto[s].cor = 2;//esta parte inicializa o ponto 
		fila(ponto,row,maior,1,s);//esta parte coloca o ponto iniciado na fila
		imprime(mat,ponto,maior,row);//esta parte imprime tudo
		busca(mat,ponto,maior,row);//esta parte faz a busca com base na fila
		
	for(i=0;i<maior;i++){//esta parte procura arestas isoladas para continuar a busca
		if(ponto[i].cor==1){
			ponto[i].cor=2;
			fila(ponto,row,maior,1,i);
			busca(mat,ponto,maior,row);
	}
}
	fclose(arq1);//fecha arquivo
	//libernado memoria
	for (i=0; i < maior; i++){ //Liberando memoria de cada coluna e dps da linha
       free(mat[i]); //esta parte liberar as memorias dentro do vetor
	}
	free(mat);//esta parte liberar as memorias
	free(ponto);
	printf("\n.......Fim....\n");
	return 1;
}

void busca(int **mat,vertice *ponto,int maior,int*row){
	int s,i,j;
	for(i=maior;i>=0;i--){//esta parte pega o ultimo da fila para executar
		if(row[i]!=-1){
			s = row[i];
			break;
		}
	}
	for(i=0;i<maior;i++){
		if((mat[s][i]==1) && (ponto[i].cor==1)){//esta parte procura os adjacentes
			ponto[i].cor=2;
			fila(ponto,row,maior,1,i);
			imprime(mat,ponto,maior,row);
			busca(mat,ponto,maior,row);
		}
	}
		//esta parte apos voltar da recursividade ele colocar a cor= cinza e tira da fila
	ponto[s].cor=3;
	fila(ponto,row,maior,2,s);
	imprime(mat,ponto,maior,row);
	return;
}


void imprime(int **mat, vertice *ponto,int maior,int* row){
	int i,j;
	printf("Fila: ");
	for(i=0;i<maior;i++){//imprime a fila
		if(row[i]==-1)//esta parte caso nao seja o numero da fila ele apenas ignora
		continue;
		else printf("%d ",row[i]);
	}
	
	printf("\n");
	for(i=0;i<maior;i++){
		for(j=0;j<maior;j++){
			printf("%d ",mat[i][j]);//esta parte imprime as arestas
		}
		printf("\n");
	}
	printf("\n\n");
	for(i=0;i<maior;i++){//esta parte imprime todos os dados
		if(ponto[i].cor==2 ||ponto[i].cor==3)
		printf("Vertice: %d\nStatus: ",i);
		if(ponto[i].cor==2)
		printf("Cinza\n");
		else if(ponto[i].cor==3)
		printf("Preto\n");
		if(ponto[i].cor==2 || ponto[i].cor==3){
		printf("Descoberta: %d\n",ponto[i].inicio);
		printf("Finalização: %d\n\n",ponto[i].final);
	}
	}
	
	system("pause");
	system("cls");
}

void fila(vertice *ponto,int* row,int maior,int p,int i){// so coloca e tira da fila
	int j,s;
	switch (p){
		case 1://esta parte do case 1 coloca na fila
			row[maior]=i;
			contador++;//esta parte adiciona ++ as posições
			ponto[i].inicio=contador;//esta parte coloca as posições de descoberta
			j=maior;
			while(row[j]!=-1 && row[j-1]==-1){//esta parte orgraniza a fila da direita para esquerda
				row[j-1]=row[j];
				row[j]=-1;
				if(j<=1)
				break;
				j--;
			}
		break;
		case 2:
			for(j=maior;j>=0;j--){
				if(row[j]!=-1){
					row[j]=-1;
					break;
				}
			}
			contador++;
			ponto[i].final=contador;//esta parte coloca as posições de finalizações
		break;
	}
	
}
