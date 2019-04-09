#include<stdio.h>
#include<pthread.h>
#include<semaphore.h>
#include<unistd.h>

sem_t mutex_c,mutex_m,common_block1,common_block2,cat_block,mice_block;
int cat_count=0,mouse_count=0;
int Num_bowls,Num_cats,Num_mouse,Num_total;
int a=0,b=0;

void *cat(void *arg)
{
	if(cat_count==Num_bowls)
	sem_wait(&mutex_c);
	
	sem_wait(&mutex_c);
    cat_count++;
    a=cat_count;
    
    if(cat_count==1)
    {
    	sem_wait(&common_block2);
    	sem_wait(&common_block1);
    }
    
    printf("\nCat %d is entering",a);
    sem_post(&mutex_c);
    if(cat_count==Num_bowls)
    {
    	printf("\ncats are eating");
        sleep(2);
        printf("\ncats are ready to leave");
        
        a=cat_count;
        cat_count = cat_count-1;
        
        printf("\nCat %d is leaving",a);
        if(cat_count==0)
        {
        	sem_post(&common_block1);
        	sem_post(&common_block2);
        }
        sem_post(&mutex_c);
    }
    
    else
    {
    	printf("\ncat is eating");
    	sleep(2);
    	printf("\ncat is ready to leave");
    	sem_wait(&mutex_c);
    	
    	a=cat_count;
    	cat_count = cat_count-1;
    	
    	printf("\ncat %d is leaving",a);
        if(cat_count==0)
        {
        	sem_post(&common_block1);
        	sem_post(&common_block2);
        }
        sem_post(&mutex_c);
    }
}

void *mouse(void *arg)
{
	if(mouse_count==Num_bowls)
	sem_wait(&mutex_m);
	
	sem_wait(&mutex_m);
    mouse_count++;
    b=mouse_count;
    
    if(mouse_count==1)
    {
    	sem_wait(&common_block1);
    	sem_wait(&common_block2);
    }
    
    printf("\nMouse %d is entering",b);
    sem_post(&mutex_m);
    if(mouse_count==Num_bowls)
    {
    	printf("\nMouse are eating");
        sleep(2);
        printf("\nMouse are ready to leave");
        
        b=mouse_count;
        mouse_count = mouse_count-1;
        
        printf("\nMouse %d is leaving",b);
        if(mouse_count==0)
        {
        	sem_post(&common_block2);
        	sem_post(&common_block1);
        }
        sem_post(&mutex_m);
    }
    
    else
    {
    	printf("\nMouse is eating");
    	sleep(2);
    	printf("\nMouse is ready to leave");
    	sem_wait(&mutex_m);
    	b=mouse_count;
    	mouse_count = mouse_count-1;
    	printf("\nMouse %d is leaving",b);
        if(mouse_count==0)
        {
        	sem_post(&common_block2);
        	sem_post(&common_block1);
        }
        sem_post(&mutex_m);
    }
}

int main()
{
	int i,x,y;
	
	printf("Enter the no of bowls");
	scanf("%d",&Num_bowls);
	
	printf("\nEnter no of cats");
	scanf("%d",&Num_cats);
	
	printf("\nEnter no of mouse");
	scanf("%d",&Num_mouse); 
	
    pthread_t cat_id[5],mice_id[5];
    sem_init(&mutex_c,0,1);
    sem_init(&mutex_m,0,1);
    sem_init(&cat_block,0,1);
    sem_init(&mice_block,0,1);
    sem_init(&common_block1,0,1);
    sem_init(&common_block2,0,1);
    
    Num_total=Num_cats+Num_mouse;
    
    for(int i=0;i<Num_total;i++)
    {
    	if(x<=3)
    	{
    		pthread_create(&cat_id[i],NULL,cat,NULL);
    		x++;
    		if(x==3)
    		y=0;
		}
		
		else
		{
			pthread_create(&mice_id[i],NULL,mouse,NULL);
			y++;
			if(y==3)
			x=0;
		}
	}
	
    for(int i=0;i<Num_total;i++)
    {
    	if(x<=3)
    	{
    		pthread_join(cat_id[i],NULL);
    		x++;
    		if(x==3)
    		y=0;
		}
		
		else
		{
			pthread_join(mice_id[i],NULL);
			y++;
			if(y==3)
			x=0;
		}
	}
   
}
