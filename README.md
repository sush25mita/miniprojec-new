#include<stdio.h>
#include<string.h>
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
        char opcd[5];
    };
    struct optb e1;
    struct symtab
    {
        char label[10];
        char addrs[6];
    };
    struct symtab s[20];
    FILE *fp,*fp1,*fp2;
    int i,j,l,symentry=0,c=0,len=0;
    char locctr[6]="0000";
    char startadr[6]="0000";
    char length[6],record[100],add[50];
    fp=fopen("input.txt","r");
    fp1=fopen("interfile.txt","w");
    fp2=fopen("optab.txt","r");
    while(fscanf(fp,"%s\t%s\t%s\n",e.lab,e.mnemo,e.field)!=EOF)
    {
        if(strcmp(e.mnemo,"START")==0)
        {
            intermediate(fp1,e.field,e.lab,e.mnemo,e.field);
            for(i=0;e.field[i]!='\0';i++)
            {
                locctr[i]=e.field[i];
                startadr[i]=e.field[i];
            }
            locctr[i]='\0';
            startadr[i]='\0';
        }
        else
        {
            if(strcmp(e.mnemo,"END")!=0)
            {
                if(strcmp(e.lab,"-")!=0)
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
                        for(i=0;locctr[i]!='\0';i++)
                            s[symentry].addrs[i]=locctr[i];
                        s[symentry].addrs[i]='\0';
                        symentry++;
                    }
                }
                intermediate(fp1,locctr,e.lab,e.mnemo,e.field);
                fp2=fopen("optab.txt","r");
                while(fscanf(fp2,"%s%s\n",e1.mne,e1.opcd)!=EOF)
                {
                    if(strcmp(e1.mne,e.mnemo)==0)
                    {
                        counter(locctr,"0003");
                    }
                }
                if(strcmp(e.mnemo,"WORD")==0)
                    counter(locctr,"0003");
                else if(strcmp(e.mnemo,"RESB")==0)
                {
                    l=0;
                    for(i=0;e.field[i]!='\0';i++)
                        l=(l*10)+e.field[i]-48;
                    dectohex(l,add);
                    counter(locctr,add);
                }
                else if(strcmp(e.mnemo,"RESW")==0)
                {
                    l=0;
                    for(i=0;e.field[i]!='\0';i++)
                        l=(l*10)+e.field[i]-48;
                    l=l*3;
                    dectohex(l,add);
                    counter(locctr,add);
                }
                else if(strcmp(e.mnemo,"BYTE")==0)
                {
                    c=0;
                    i=2;
                    if(e.field[0]=='X')
                    {
                        while(e.field[i]!='\0')
                        {
                            c++;
                            i++;
                        }
                        c--;
                        c=c/2;
                    }
                    else if(e.field[0]=='C')
                    {
                        while(e.field[i]!='\0')
                        {
                             c++;
                             i++;
                        }
                        c--;
                    }
                    dectohex(c,add);
                    counter(locctr,add);
                }
            }
            else
                intermediate(fp1,"-",e.lab,e.mnemo,e.field);
        }
    }
    for(i=3;i>=0;i--)
    {
        length[i]=((locctr[i]-48)-(startadr[i]-48))+48;
    }
    length[4]='\0';
    fclose(fp);
    fclose(fp1);
    fp1=fopen("interfile.txt","r");
    fp=fopen("objectprog.txt","w");
    struct inter
    {
        char loctr[5];
        char labl[10];
        char mnem[10];
        char fiel[10];
    }ee;
    while(fscanf(fp1,"%s%s%s%s\n",ee.loctr,ee.labl,ee.mnem,ee.fiel)!=EOF)
    {
        if(strcmp(ee.mnem,"START")==0)
        {
            record[0]='H';
            record[1]='^';
            j=2;
            for(i=0;ee.labl[i]!='\0';i++)
            {
                record[j]=ee.labl[i];
                j++;
            }
            while(j<=7)
            {
                record[j]=' ';
                j++;
            }
            record[j]='^';
            record[9]=record[10]='0';
            j=11;
            for(i=0;startadr[i]!='\0';i++)
            {
                record[j]=startadr[i];
                j++;
            }
            record[j]='^';
            record[16]=record[17]='0';
            j=18;
            for(i=0;length[i]!='\0';i++)
            {
                record[j]=length[i];
                j++;
            }
            record[j]='\0';
            fprintf(fp,"%s\n",record);
            j=0;
        }
        else
        {
            if(strcmp(ee.mnem,"END")!=0)
            {
                if(j>75)
                {
                    c=0;
                    if(strcmp(ee.mnem,"BYTE")==0)
                    {
                        if(ee.fiel[0]=='X')
                        {
                            if((strlen(ee.fiel)-3)>(81-j))
                                c=1;
                        }
                        else if(ee.fiel[0]=='C')
                        {
                            if((strlen(ee.fiel)*2)>(81-j))
                                c=1;
                        }
                    }
                    else
                        c=1;
                    if(c==1)
                    {
                        len=len/2;
                        dectohex(len,add);
                        record[9]=add[2];
                        record[10]=add[3];
                        record[11]='^';
                        j--;
                        record[j]='\0';
                        fprintf(fp,"%s\n",record);
                        j=0;
                    }
                }
                if(j==0)
                {
                    for(i=0;i<83;i++)
                        record[i]=' ';
                    record[0]='T';
                    record[1]='^';
                    record[2]=record[3]='0';
                    j=4;
                    for(i=0;ee.loctr[i]!='\0';i++)
                    {
                        record[j]=ee.loctr[i];
                        j++;
                    }
                    record[8]='^';
                    j=12;
                }
                fp2=fopen("optab.txt","r");
                while((fscanf(fp2,"%s%s\n",e1.mne,e1.opcd))!=EOF)
                {
                    if(strcmp(e1.mne,ee.mnem)==0)
                    {
                        for(i=0;e1.opcd[i]!='\0';i++)
                        {
                            record[j]=e1.opcd[i];
                            len++;
                            j++;
                        }
                    }
                }
                for(i=0;i<symentry;i++)
                {
                    if(strcmp(s[i].label,ee.fiel)==0)
                    {
                        for(l=0;s[i].addrs[l]!='\0';l++)
                        {
                            record[j]=s[i].addrs[l];
                            len++;
                            j++;
                        }
                    }
                }
                if(strcmp(ee.mnem,"WORD")==0)
                {
                    l=strlen(ee.fiel);
                    for(i=0;i<(6-l);i++)
                    {
                        record[j]='0';
                        len++;
                        j++;
                    }
                    for(i=0;ee.fiel[i]!='\0';i++)
                    {
                        record[j]=ee.fiel[i];
                        len++;
                        j++;
                    }
                }
                else if(strcmp(ee.mnem,"BYTE")==0)
                {
                    if(ee.fiel[0]=='X')
                    {
                        for(i=0;ee.fiel[i]!='\0';i++)
                        {
                            record[j]=ee.fiel[i];
                            len++;
                            j++;
                        }
                    }
                    if(ee.fiel[0]=='C')
                    {
                        for(i=0;ee.fiel[i]!='\0';i++)
                        {
                            if((ee.fiel[i]>='a'&&ee.fiel[i]>='z')||(ee.fiel[i]>='A'&&ee.fiel[i]>='Z'))
                            {
                                l=ee.fiel[i];
                                dectohex(l,add);
                                record[j]=add[2];
                                len++;
                                j++;
                                record[j]=add[3];
                                len++;
                                j++;
                            }
                        }
                    }
                }
                record[j]='^';
                j++;
            }
            else
            {
                len=len/2;
                dectohex(len,add);
                record[9]=add[2];
                record[10]=add[3];
                record[11]='^';
                j--;
                record[j]='\0';
                fprintf(fp,"%s\n",record);
                for(i=0;i<83;i++)
                    record[i]=' ';
                record[0]='E';
                record[1]='^';
                record[2]=record[3]='0';
                j=4;
                for(i=0;startadr[i]!='\0';i++)
                {
                    record[j]=startadr[i];
                    j++;
                }
                record[j]='\0';
                fprintf(fp,"%s\n",record);
            }
        }
    }
}
void intermediate(FILE *fp,char loc[],char la[],char mn[],char fi[])
{
    int i;
    struct inter
    {
        char loctr[5];
        char labl[10];
        char mnem[10];
        char fiel[10];
    }ee;
    for(i=0;loc[i]!='\0';i++)
        ee.loctr[i]=loc[i];
    ee.loctr[i]='\0';
    for(i=0;la[i]!='\0';i++)
        ee.labl[i]=la[i];
    ee.labl[i]='\0';
    for(i=0;mn[i]!='\0';i++)
        ee.mnem[i]=mn[i];
    ee.mnem[i]='\0';
    for(i=0;fi[i]!='\0';i++)
        ee.fiel[i]=fi[i];
    ee.fiel[i]='\0';
    fprintf(fp,"%s\t%s\t%s\t%s\n",ee.loctr,ee.labl,ee.mnem,ee.fiel);
}
void counter(char count[],char ad[])
{
    int l,c,i,n;
    for(i=3;i>=0;i--)
    {
        if(ad[i]>='A'&&ad[i]<='F')
            n=ad[i]-55;
        else
            n=ad[i]-48;
        if(count[i]>='A'&&count[i]<='F')
            l=count[i]-55;
        else
            l=count[i]-48;
        l=l+n;
        if(l>15)
        {
            c=0;
            while(l>15)
            {
                l=l-16;
                c++;
            }
            count[i-1]=count[i-1]+c;
        }
        if(l>=0&&l<=9)
            count[i]=l+48;
        else if(l>=10&&l<=15)
        {
            count[i]=l+55;
        }
    }
}
void dectohex(int dec,char hex1[])
{
    int i=0,n,j,k;
    char hex[6];
    while(dec!=0)
    {
        n=dec%16;
        if(n>=10&&n<=15)
            hex[i]=n+55;
        else
            hex[i]=n+48;
        i++;
        dec=dec/16;
    }
    hex[i]='\0';
    k=0;
    for(j=3;j>=0;j--)
    {
        if(k<i)
        {
            hex1[j]=hex[k];
            k++;
        }
        else
            hex1[j]=48;
    }
    hex1[4]='\0';
}
