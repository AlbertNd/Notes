# Creation d'un selecte avec des option lieés a un autre select (Je e comprend)

1. Création de la base des données, model, controller et migration des tables 
    1. **Pays**
        - model, controller, migration 
        - `php artisan make:model pays -mc`
            - migration 
            ```
                Schema::create('pays', function (Blueprint $table) {
                    $table->id();
                    $table->string('name');
                    $table->timestamps();
                });
            ```
            - model
            ```
                protected $fillable = [
                    'name'
                ];
            ```

    2. **Villes** 
        - model, controller, migration 
        - `php artisan make:model ville -mc`
            - migration
            ```
                Schema::create('villes', function (Blueprint $table) {
                $table->id();
                $table->string('name')->nullable();
                $table->foreignId('pays_id')->constrained();
                $table->timestamps();
                });
            ```
            - model
            ```
                protected $fillable = [
                    'pays-id',
                    'name'
                ];

                public function pays()
                {
                    return $this->belongsTo(pays::class);
                }
            ```
    3. Dans le contoller **VilleController**
        ```
            public function index()
            {
                $ville = ville::whereHas('pays', function ($query) {
                    $query->whereId(request()->input('pays-id', 0));
                })->pluck('name', 'id');

                return response()->json($ville);
            }
        ``` 
    4. Lanceement de la migration
        - `php artisan migrate`