Pada project ini kami mencoba untuk membuat sebuah database pemain sepak bola dimana user dapat memasukan beberapa informasi antara lain(nama, performance, stamina) dan meampilkan statistik pemain dengan menampilkan sebuah chart, dengan menggunakan fitur tambahan pada Dev C++ yaitu OpenGL, yang memungkinkan kita untuk dapat menampilkan visual, beberapa lagkah yang harus dilakukan antara lain :
    1. Install library tambahan untuk fitur OpenGL
        https://www.transmissionzero.co.uk/files/software/development/GLUT/freeglut-MinGW-3.0.0-1.mp.zip
        ikuti langkah - langkah yang tertera pada panduan.
    2. Set Up pada software Dev C++ :
        - Buka Software Dev C++
        - Pilih "New Project"
        - Pilih Menu "Project Option"
        - Pilih "Parameter"
        - Copy pada bagian linker "-lopengl32 -lfreeglut -lglu32"
    3. Masukan Source Code dibawah
    
Catatan : pada program ini masih belum berhasil mengintegrasikan informasi yang diberikan oleh user menjadi chart, chart yang dimunculkan adalah data yang dimasukan pada bagian "Program Display OpenGL", dimana pada grafik yang ditampilkan menggunakan sistem koordinat, dengan nilai 0.50 adalah titik pusat grafik.
    
    
  
    /*Headers*/
    #include <GL/glut.h>
    #include<stdio.h>
	#include<stdlib.h>
	#include<conio.h>
	#include<string.h>
    /*End of headers*/
    /* Check Compiler*/
    #ifdef __FLAT__
    #include <Windows.h>
    #endif 
    /*End of Check*/
    /*Preloaded  Functions*/
    void myInit(void)
    {
        glClearColor(1.0, 1.0, 1.0, 0.0);
        glShadeModel(GL_FLAT);
         
    }
    //program display Open GL
    void myDisplay(void)
    {
        glClear(GL_COLOR_BUFFER_BIT);
        glColor3f(1.0, 0.0, 0.0); // warna yang digunakan
        glBegin(GL_POLYGON); // penggambaran diagram dengan menggunakan koordinat, koordinat titik pusat adalah Y,X (0.50 , 0.50)
            glVertex2f(0.90, 0.50); //kanan, nilai 0.50 < x < 1.00
            glVertex2f(0.50 , 0.90); //atas, nilai 0.50 < x < 1.00
            glVertex2f(0.05 , 0.50); //kiri, nilai 0.00 < x < 0.50
            glVertex2f(0.50 , 0.10); //bawah, nilai 0.00 < x < 0.50
        glEnd();
        glutSwapBuffers();
    }
    /*End of preloaded functions*/
    int main(int argc, char** argv)
    {
    	FILE *fp, *ft; // pembuatan file pada program ini
	char another, choice, help;
	struct ply //structure yang menunjukkan pemain
	{
		char name [40]; //nama pemain
		int age; //umur pemain
		float rating; //rating pemain
		
		
	};
	
         
	struct ply p; //pembentukan variable dari pemain
	char plyname[40];
	long int recsize; //ukuran dari setiap data yang terdata

	/** pencarian file pada folder dengan nama PLY.DAT
	*jika PLY.DAT ada maka program hanya akan mebaca dan mendata ulang saja
	*jika tidak maka program akan membuat file baru dengan nama PLY.DAT
	*/
	fp = fopen ("PLY.DAT","rb+"); // rb+ menyatakan perintah read file program yag ada
	if(fp == NULL)
	{
		fp = fopen("PLY.DAT","wb+"); // wb+ menyatakan write file program 
		if(fp == NULL)
		{
			printf("Cannot Open File");
			exit(1);
		}
	}
	recsize = sizeof(p);
	while(1)
	{
		system("cls"); //membersihkan layar pada program yang akan dijalankan, menghapus data yang telah dijalankan tanpa harus menutup program
		printf("RECORD DATA BASE PEMAIN BOLA\n\n");
		printf("1. Add Data\n\n");
		printf("2. List Data\n\n");
		printf("3. Modify Data\n\n");
		printf("4. Delete Data\n\n");
		printf("5. Help\n\n");
		printf("6. Exit\n\n");
		printf("Your choice : "); //masukkan pilihan menu
		fflush(stdin);
		choice = getche();
		switch(choice)
		
		{
			case '1': //jika user memilih main menu 1
				system("cls");
				fseek(fp,0,SEEK_END);
				another = 'y';
				while (another == 'y') //saat user ingin memassukkan data yang baru
				{
					printf("\nEnter Name : ");
					scanf("%[^\n]s", &p.name);
					printf("\nEnter Performance : ");
					scanf("%d", &p.age);
					printf("\nEnter Stamina : ");
					scanf("%f", &p.rating);
					
					fwrite(&p,recsize,1,fp);
					
					printf("\nAdd another data(y/n) ");
					fflush(stdin);
					another = getche();
				}
				break;
			case '2': //saat user memilih main menu ke 2
				system("cls");
				rewind(fp);
				while(fread(&p,recsize,1,fp)==1)
				{
					printf("\n%s %d %.1f", p.name,p.age,p.rating);
				}
				getch();
				break;
			case '3': // jika user memilih main menu ke 3
				system("cls");
				another = 'y';
				while(another == 'y')
				{
					printf("Enter the player name to modify: ");
					scanf("%[^\n]s", &plyname);
					rewind(fp);
					while(fread(&p,recsize,1,fp)==1)
					{
						if(strcmp(p.name,plyname) == 0)
						{
							printf("\nEnter new name, age and rating : ");
							scanf("%s%d%f",&p.name,&p.age,&p.rating);
							fseek(fp,-recsize,SEEK_CUR);
							fwrite(&p,recsize,1,fp);
							break;
						}
					}
					printf("\nModify another data(y/n)");
					fflush(stdin);
					another = getche();
				}
				break;
			case '4': // jika user memilih main menu ke 4
				system("cls");
				another = 'y';
				while(another == 'y')
				{
					printf("\nEnter name of player to delete : ");
					scanf("%[^\n]s",plyname);
					ft = fopen("Temp.dat","wb");
					rewind(fp);
					while(fread(&p,recsize,1,fp) == 1)
					{
						if(strcmp(p.name,plyname) != 0)
						{
							fwrite(&p,recsize,1,ft);
						}
					}
					fclose(fp);
					fclose(ft);
					remove("PLY.DAT");
					rename("Temp.dat","PLY.DAT");
					fp = fopen("PLY.DAT", "rb+");
					printf("Delete another data(y/n)");
					fflush(stdin);
					another = getche();
				}
				break;
			case '5': // jika user memilih main menu ke 5
				system("cls");
				printf("Help\n\n");
				printf("1. How to Input the data\n\n");
				printf("2. How to Show the data\n\n");
				printf("3. How to Modify the data\n\n");
				printf("4. How to delete the data\n\n");
				fflush(stdin);
				help = getche();
				switch(help)
				{
					case '1':
						system("cls");
						printf("first type '1' in the main menu options\n\n");
						printf("then input the name of the player\n\n");
						printf("then input the age of the player\n\n");
						printf("and the last input the rating of the player\n\n");
						printf("if you wanna put another data type 'y' in the question 'add another data'");
						getch();
						break;
					case '2':
						system("cls");
						printf("type '2' in the main menu options\n\n");
						printf("and the data was input all shown");
						getch();
						break;
					case '3':
						system("cls");
						printf("type '3' in the main menu options\n\n");
						printf("type the player name you want to modify\n\n");
						printf("then input the data with this format 'Name Age Rating' ");
						printf("if you wanna modify another data type 'y' in the question 'add another data'");
						getch();
						break;
					case '4':
						system("cls");
						printf("type '4' in the main menu options\n\n");
						printf("input the name of player you want to delete");
						printf("if you wanna delete another data type 'y' in the question 'add another data'");
						getch();
						break;
				}
				break;
			case '6':
				fclose(fp);
				exit(0);	
		}
		glutInit(&argc, argv);
        glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB);
        glutInitWindowSize(600, 600);
        glutInitWindowPosition(100, 100);
        glutCreateWindow("2D Open GL"); //judul pada windows yang ditampilkan
		myInit();
		glutDisplayFunc(myDisplay);
        glutMainLoop();	

	}
        
        return 0;
    }
    
