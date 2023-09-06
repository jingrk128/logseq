- ```
      int T;
      char S[10000] = {0};
      
      scanf("%d", &T);
      
      
      for(int j=0; j<T; j++)
      {
          scanf("%*c");
          
          scanf("%s", S);
      
          int len = strlen(S);
          for(int i=0; i<len; i+=2)
              printf("%c", S[i]);
          
          printf(" ");
          
          for(int i=1; i<len; i+=2)
              printf("%c", S[i]);
              
          printf("\n");
          
      }
  ```