# Création d'un systeme de QCM 

1. Il faut au moins 5 table :
    1. **La table des chapitre** 
        - Un chapitre, et le parent des qustions, il peut avoir plusieurs questions *(Les enfants)* 
        - une question ne peut pas etre dans plusieurs chapitre 
        1. **Relation One-To-Many** Avec la table questions 
            1. Dans la table parent *(table sujet ou catégories)*
                - Appel de la fonction question et qui retourne la methode ***hasMany***
                    ```
                        public function Question():HasMany {
                            return $this->hasMany(Question::class)
                        }
                    ``` 
    2. **La table des questions** 
        - Une question, est le parent des options de reponses, et peut avoir 3 options dont une bonne reponse
        1. **Relation One-To-Many** avec la tables des options
            1. Dans la table parent *(table question)*
                - Appel de la fonction Options et qui retourne la methode ***hasMany***
                    ```
                        public function Options():HasMany {
                            return $this->hasMany(Options::class)
                        }
                    ``` 
    3. **La table des options de reponses** 
        - Dans une option il y a une bnne reponse *(Une bonne reponse à aprtient à un ensemble des options)* 
        1. **Relation One-To-One** avec la table des reponses 
            1. Dans la table options
                - Appel de la fonction reponse et qui retourne la methode ***hasOne***
                    ```
                        public function Reponses():HasOne {
                            return $this->hasOne(Reponses::class)
                        }
                    ``` 
    4. **la tables des Reponses**
        - Chaque bonne reponse à une explication 
        1. **Relation One-To-One** avec la table Explication 
            1. Dans la table Reponse
                - Appel de la fonction explication et qui retourne la methode ***hasOne***
                    ```
                        public function Reponses():HasOne {
                            return $this->hasOne(Reponses::class)
                        }
                    ``` 
    5. **la table d'explication de la bonne reponses**