# miniprojec-new
#include<stdio.h>
void word(char[],char[],int);
int main()
{
    FILE *fp,*fp1;
    char str[80],opcode[10],operand[10],label[10]symtab[10][10];
    int c=0,i,j=0,locctr=0,l,symentry=0;
    fp=fopen("prog.txt","w");
    if(fp==NULL)
    {
        puts("cannot open file");
        exit(0);
    }
    while(strlen(gets(str))>0)
    {
        fputs(str,fp);
        fputs("\n",fp);
    }
    fclose(fp);
    fp=fopen("prog.txt","r");
    fp1=fopen("interfile.txt","w");
    if(fp==NULL)
    {
        puts("cannot open file");
        exit(0);
    }
    while(fgets(str,79,fp)!=NULL)
    {
        word(str,opcode,6);
        if(strcmp(opcode,"START")==0)
        {
            word(str,operand,12);
            l=strlen(operand);
            j=0;
            while(j<l-1)
            {
                locctr=(locctr*10)+(operand[j]-48);
                j++;
            }
            intermediate(fp1,locctr,str);
        }
        while(strcmp(opcode,"END")!=0)
        {
            if(str[0]!='.')
            {
                word(str,label,0);
                if(strcmp(label,' ')!=0)
                {
                    c=i=0;
                    while(i!=symentry)
                    {
                        word(symtab[i],operand,0);
                        if(strcmp(operand,label)==0)
                            c++;
                        i++;
                    }
                    if(c==0)
                    {
                        l=strlen(label);
                        for(i=0;label[i]!='\0';i++)
                        {
                            symtab[symentry][i]=label[i];
                        }
                        while(i!=6)
                        {
                            symtab[symentry][i]=' ';
                            i++;
                        }
                        symentry++;
                    }
                }
            }
        }

    }
    fclose(fp);

}
void word(char str[],char wrd[],int n)
{
    int i,j=0;
    for(i=n;(str[i]!=' '&&str[i]!='\0');i++)
    {
        wrd[j]=str[i];
        j++;
    }
    wrd[j]='\0';
}
void intermediate(FILE *fp,int loc,char str[])
{
    int i,j=0,n;
    char line[30];
    i=3;
    while(i!=-1)
    {
        n=loc%10;
        line[i]=n+48;
        loc=loc/10;
        i--;
    }
    line[4]=' ';
    line[5]=' ';
    i=6;
    for(j=0;str[j]!='\0';j++)
    {
        line[i]=str[j];
        i++;
    }
    line[i]='\0';
    fputs(line,fp);
    fputs("\n",fp);
}
