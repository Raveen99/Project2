#include<stdio.h>
#include<semaphore.h>
#include<pthread.h>
#include<unistd.h>
sem_t mutex_cat,mutex_mice;
int NumBowl = 0,Numcat = 0,Nummouse=0;
int c1,m1,c2,m2;

void *cat(void *arg)
{
  sem_wait(&mutex_cat);
  Numcat = Numcat + 1;
  
  c1=Numcat;
  
  if(Numcat==1)
  sem_wait(&mutex_mice);
  printf("\nCat %d is entering",c1);
  sem_post(&mutex_cat);
  NumBowl=NumBowl+1;
  printf("\ncat %d is eating bowl %d",c1,NumBowl);
  sleep(1);
  
  sem_wait(&mutex_cat);
  c2=Numcat;
  Numcat = Numcat - 1;
  printf("\nCat %d is leaving",c2);
  if(Numcat==0)
   sem_post(&mutex_mice);
  sem_post(&mutex_cat);
}

void *mice(void *arg)
{
    sem_wait(&mutex_mice);
    Nummouse = Nummouse + 1;
  
    m1=Nummouse;
  
    if(Nummouse==1)
    sem_wait(&mutex_cat);
    printf("\nMouse %d is entering",m1);
    sem_post(&mutex_mice);
    NumBowl=NumBowl+1;
    printf("\nMouse %d is eating bowl %d",m1,NumBowl);
    sleep(1);
  
    sem_wait(&mutex_mice);
    m2=Nummouse;
    Nummouse = Nummouse - 1;
    printf("\nMouse %d is leaving",m2);
    if(Nummouse==0)
    sem_post(&mutex_cat);
    sem_post(&mutex_mice);
  
}
int main()
{
  int i,b;
  int NumCats,NumMice; 
  pthread_t cat_id[20],mice_id[20];
  sem_init(&mutex_cat,0,1);
  sem_init(&mutex_mice,0,1);
  printf("Enter the number of cats :\n");
	scanf("%d",&NumCats);
	printf("Enter the number of mice :\n");
	scanf("%d",&NumMice);
  for(i=0;i<NumCats;i++)
  {
    pthread_create(&cat_id[i],NULL,cat,NULL);
    
  }
  for(i=0;i<NumMice;i++)
  {
    pthread_create(&mice_id[i],NULL,mice,NULL);
    
  }
  for(i=0;i<NumCats;i++)
  {
    pthread_join(cat_id[i],NULL);
    
  }
   for(i=0;i<NumMice;i++)
  {
    pthread_join(mice_id[i],NULL);
    
  }
  return 0;
}
