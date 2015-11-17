#include<stdio.h>
int main()
{
    struct prog
    {
        char lab[10];
        char mnemo[10];
        char field[10];
    }e;
    struct optb
    {
        char mne[10];
        int opcd;
    };
    struct optb e1;
    struct symtab
    {
        char label[10];
        int addrs;
    };
    struct symtab s[20];
    FILE *fp,*fp1,*fp2;
    int i,j,locctr=0,l,symentry=0,c=0;
    fp=fopen("input.txt","r");
    fp1=fopen("interfile.txt","w");
    fp2=fopen("optab.txt","r");
    while(fscanf(fp,"%s\t%s\t%s\n",e.lab,e.mnemo,e.field)!=EOF)
    {
        if(strcmp(e.mnemo,"start")==0)
        {
            l=strlen(e.field);
            j=0;
            while(j<l)
            {
                locctr=(locctr*10)+(e.field[j]-48);
                j++;
            }
            intermediate(fp1,locctr,e.lab,e.mnemo,e.field);
        }
        else
        {
            if(strcmp(e.lab,"END")!=0)
            {
                /*if(strcmp(e.lab,"-")!=0)
                {
                    c=0;
                    for(i=0;i<symentry;i++)
                    {
                        if(strcmp(s[i].label,e.lab)==0)
                            c++;
                    }
                    if(c==0)
                    {
                        for(i=0;e.lab[i]!='\0';i++)
                            s[symentry].label[i]=e.lab[i];
                        s[symentry].label[i]='\0';
                        symentry++;
                    }
                }*/
                intermediate(fp1,locctr,e.lab,e.mnemo,e.field);
                while(fscanf(fp2,"%s%d\n",e1.mne,&e1.opcd)!=EOF)
                {
                    if(strcmp(e1.mne,e.mnemo)==0)
                    {
                        locctr=locctr+3;
                    }
                }
                if(strcmp(e.mnemo,"WORD")==0)
                    locctr=locctr+3;
            }
        }
    }
}
void intermediate(FILE *fp,int loc,char la[],char mn[],char fi[])
{
    int i;
    struct inter
    {
        int loctr;
        char labl[10];
        char mnem[10];
        char fiel[10];
    }ee;
    ee.loctr=loc;
    for(i=0;la[i]!='\0';i++)
        ee.labl[i]=la[i];
    ee.labl[i]='\0';
    for(i=0;mn[i]!='\0';i++)
        ee.mnem[i]=mn[i];
    ee.mnem[i]='\0';
    for(i=0;fi[i]!='\0';i++)
        ee.fiel[i]=fi[i];
    ee.fiel[i]='\0';
    fprintf(fp,"%d\t%s\t%s\t%s\n",ee.loctr,ee.labl,ee.mnem,ee.fiel);
}
