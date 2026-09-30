----------------------------------------

Working notes

Wilken Aldair Misael, PhD
IPR/PUCRS

----------------------------------------

Terça, 15 de setembro de 2026:

Hoje realizamos a reunião quinzenal, onde me foi introduzida a dinamica do projeto e foram dadas algumas atualizações dos projetos atuais. Também foi passada a tarefa de analisar os recursos computacionais, a ser feito com a Camila C.

Depois da reunião nós discutimos um pouco sobre o que poderemos entregar durante esse ciclo, ela me passou alguns artigos introdutórios da parte de MD.

Também enviamos a solicitação de recursos computacionais de forma remota, e já obtivemos.

- [x] Instalar o grommacs, pymol, vmd
- [x] Ler o artigo sobre MD
- [x] Rodar primeiros calculos de MD

----

Quarta, 16 de setembro de 2026:

- [x] Ler o artigo sobre MD
- [x] Determinar as atividades que serão feitas até o próximo ciclo

Capacitação em GROMACS — 10h
Primeiros cálculos de propriedades de transporte — 20h
Revisão da literatura sobre simulação de eletrólitos para baterias Li-metal — 20h
Definição das frentes de trabalho de curto e médio prazo — 10h

- [x] Formação a tarde


- [x] Atualizar atividades no monday

----

Quinta, 17 de setembro de 2026:

- [x] Ler o artigos sobre HEMs

Fazer review rapida de:

    dft baterias em high entropy materials, foco em baterias de co2

- [x] Fazer simulacoes de eletrolitos

[notes](/Users/wilken/Documents/PUCRS/simulations/gromacs/gromacs_li_water_tutorial/notes.md)


----

Sexta, 18 de setembro de 2026:

Evento: salsipão

- [x] Fazer simulacao de LiCl

----

Segunda, 21 de setembro de 2026:

- [x] Planning semanal

- [x] Começar a trabalhar no github do projeto IPR

- [x] Listar especificações do desktop, máquina interna e fazer lista de softwares que preciso

    Resumo de Recursos de Computação

        Pedi pro Gemini fazer um resumo completo dessas configurações.


    Na maquina remota temos:

        Gaussian16/GaussView: executei testes para o CO2 tanto usando a interface grafica quando a linha de comando. Para executar no terminal temos que fazer o seguinte:

                 # 1. Define o caminho do diretório do Gaussian
                $env:GAUSS_EXEDIR = "C:\g16w"

                # 2. Executa o cálculo com o operador &
                & "C:\g16w\g16.exe" .\co2_calc.gjf .\co2_calc.out 
        
        ainda não sei se posso adicionar esse path as variaveis de ambiente.


        ! GROMACS (nao funciona, e precisamos de algo como Windows Subsystem for Linux (WSL) ), Orca 5, SIESTA

        orca 5: funciona muito bem, ainda há algumas questões relativas a performance da paralelização a serem testadas. rodei calculos de otimizacao e fiz alguns testes para aimd. há questões adicionais que sumarizei nas notas na maquina remota.

        Materials studio para visualização: ainda não entendi muito bem onde poderemos usar esse software.

    No meu PC preciso de:

        Vim, conda

        Zotero, VSCode (ja tem na maquina virtual), 
        Visualização: VMD, pymol, avogrado, Vesta, Xcrysden, ovito
        QM, MD: GROMACS, LAMMPS, Quantum Espresso, 

- [x] Instalar LAMMPS

- [x] Instalar Quantum Espresso

- [x] Conversar com o Victor sobre:

    Os softwares que quero, o monitor e as horas extras que tenho

    => ele me passou as instruções. tomar ação sobre.

-----

Terça, 22 de setembro de 2026:

Formação: jeito puc de ser e acolher às 15h, saio mais cedo 16h30 e já desconto 30 min das horas extras que tenho.

- [x] Trabalhar na review do artigo da Moyra

- [x] começar os calculos no qe

-----

Quarta, 23 de setembro de 2026:

Reunião no teams sobre reagentes.

- [x] Plotar os resultados do qe

- [ ] Fazer os cálculos para Li-CO2

    refazer os calculos todos usando os efeitos de longa distancia:
          vdw_corr = 'd3bj'

- [x] Trabalhar na review do artigo da Moyra

    ler o artigo do maycon

        ver o que tem na literatura sobre o NASICON

- [x] Reunião com a Camila sobre os softwares


------

Quinta, 24 de setembro de 2026:

- [x] Ler o projetinho do KTH sobre eletrolitos solidos

- [x] Investigar os calculos do QE

    o problema não era com a estrutura, pois tirando a flag das correções de van der waals, o calculo começou a convergir na 5a casa decimal

    agora estou investigando usando um convergence threshold menor para a densidade eletronica e sampleando menos k-points.

    com menos k-points o cálculo terminou em aproximadamente 30 minutos. 

    Conclusões:

        Os resultados pra SCF e NSCF divergiram quanto a E_fermi. E o plot pra densidade eletronica media estavam muito deslocados para maiores valores. Vou reiniciar esses calculos considerando diferentes parametros, também vou procurar um outro pseudopotencial.

    a tarde voltei nessa questao, usando o mesmo potencial, mas mudando os parametros de corte, smearing e diminuindo a célula unitária também. 
    
    usando parametros menos apertados, celulas menores, consegui convergir a slab com pouca variacao entre os valores de fermi no scf e nscf, fiz os plots para estrutura de bandas e dos. 

- [x] Revisar questões da discussão com o Victor

    a questão da foto ainda vai ser vista

    sobre o patrimonio, conversando com a camila entendi que posso instalar programas na minha máquina sem precisar submeter nenhum formulário

-----

Sexta, 25 de setembro de 2026:

- [x] Investigar calculos qe

    ontem a noite comecei a trabalhei nos calculos da slab (montei input, geometria), mas nao consegui convergir a relaxacao.

    agora de manha revisei questao de paralelismo, e a seguinte configuracao parece funcionar bem:

        export OMP_NUM_THREADS=1
        export OPENBLAS_NUM_THREADS=1
        export VECLIB_MAXIMUM_THREADS=1

        # Execução com 4 pools de k-points
        mpirun -np 4 pw.x -npool 4 -in 05_li100_co2_adsorption_relax.in | tee 05_li100_co2_adsorption_relax.out

    os calculos ainda nao convergiram. mesmo quando se aproxima de um minimo do nada o sistema desvia, por exemplo:

        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.37981384 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38233450 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38302871 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38326783 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38335405 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38338344 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38340261 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.38340025 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.42930759 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.35170184 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.45369627 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.52250357 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.60944687 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.69091043 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.81108985 Ry
        05_li100_co2_adsorption_relax.out:!    total energy              =    -409.98941976 Ry

- [x] Ler sobre NVPs

--------

Segunda, 28 de setembro de 2026:

- [x] Modelagem Li100

    Discutindo com o gemini cheguei a conclusao de que o modelo 225 com co2 adsorvido poderia ter problemas de convergência dado a possíveis artefatos dos orbitais de co2 que estao na fronteira das pbc. Segundo o gemini:


        O que você estava observando com a célula 2x2 é um problema clássico de interação lateral artificial (efeito de alta cobertura). Quando o modelo computacional é pequeno demais, a molécula interage fortemente com as suas próprias "imagens fantasmas" nas células vizinhas, o que desestabiliza os orbitais e distorce a energia de adsorção.
    
    Um modelo com 9 átomos na superfície seria mais representativo, porém não vou conseguir rodar na minha máquina facilmente, e vou deixar esse cálculo em stand by.

    Tive um problema com a compilação do quantum espresso. Basicamente eu havia compilado na página de download e apaguei o diretório. Perdi um tempinho tentando compilar de novo, e pra que isso não ocorra novamente, a seguir informo o procedimento que adotei a partir do download dos executáveis na página do qe.

        # Crie e entre no diretório de build
        mkdir build
        cd build

        # Configure o build com GCC/GFortran e MPI ativos
        cmake .. \
        -DCMAKE_C_COMPILER=gcc \
        -DCMAKE_Fortran_COMPILER=gfortran \
        -DQE_ENABLE_MPI=ON \
        -DQE_ENABLE_OPENMP=ON

        # Compile os executáveis utilizando múltiplos núcleos
        make -j4

        # Adiciona o caminho do Quantum ESPRESSO ao final do seu .zshrc
        echo 'export PATH="/Users/wilken/qe-7.5/build/bin:$PATH"' >> ~/.zshrc

        # Recarrega o arquivo de configuração para aplicar na sessão atual
        source ~/.zshrc

    De tarde a Camila me apreesntou a forma de usar o QE no windows. Na minha máquina temos 20 processadores. Não houve ganho significativo na performance do cálculo com 20 processadores, na verdade 5 min a mais em relação ao cálculo com 8 processadores no meu mac. A diferença vem daqui:

        Parallel version (MPI & OpenMP), running on      64 processor cores
        Number of MPI processes:                 8
        Threads/MPI process:                     8

    Para evitar degradação do meu computador, preciso colocar a seguinte flag antes de rodar o cálculo:

        export OMP_NUM_THREADS=1

    Enfim, rodei o cálculo 333 na minha máquina e agora gerei uma estrutura pra amanhã relaxar a estrutura de CO2@Li100. O próximo passo vai ser extrair propriedades.

- [x] Planejar tarefas para a próxima semana e o que vou reportar na próxima reunião

    Na reunião de amanhã vou reportar:

        Num primeiro instante pensamos em modelar as propriedades (de transporte) de eletrólitos. Após compartilharmos alguns artigos, dada a experiencia dela, a camila se aprofundou mais na revisão da literatura para obter parametros para possíveis simulações. No meu lado, dei os meus primeiros passos em dinâmica molecular, aprendendo a usar o GROMACS, fazendo cálculos em pequenos sistemas como Li@H2O e LiCl@H2O, com e sem campo elétrico externo. 

        Um dos meus objetivos para as próximas semanas é começar a usar o LAMMPS, que é mais utilizado na modelagem de baterias. Apesar de existirem artigos usando o GROMACS para modelagem de baterias, ele é mais usado no contexto de biomoléculas. A maior vantagem do GROMACS é em relação a paralelização, mas o LAMMPS é mais flexível para o uso com bibliotecas externas, assim como há mais modulos que podemos explorar para obter propriedades de materiais.
        
        Em relação aos cálculos ab initio, dei uma revisada na literatura de estado sólido, e comecei a fazer alguns cálculos no quantum espresso para Li100 para reaprender os parametros desses calculos. Infelizmente na minha máquina fiquei limitado apenas a uma fatia e os calculos envolvendo a adsorcao de co2 não convergiram. Para continuar nessa tarefa preciso de mais recursos computacionais.

            Ontem comecei a fazer cálculos no Desktop.

        Em relação as nossas máquinas, dei uma primeira olhada nos softwares que temos disponíveis. Rodei alguns testes de cálculo ab initio no Gaussian e no ORCA. Uma possibilidade seria realizar cálculos qm/mm no Gaussian, com um módulo chamado ONIOM o que vou explorar nas próximas semanas. Além disso, discutindo com a Camila decidimos que seria interessante instalar o GROMACS (que aparentemente tem um diretório com ele no pc) e Quantum Espresso - ambos gratuitos, amplamente usados, flexíveis, bem documentados e podemos usar enquanto uma licensa para o VASP não está disponível. Discutimos algumas possibilidades de máquinas, de distribuidores (versatus, sdc, silix, nuv, por exemplo) no meu caso ab initio vou precisar de no mínimo 32 processadores. A camila tem mais informações dessa parte.

        Também dei uma lida na parte de HEM e em relação aos NVPs, dei uma olhada no artigo que foi submetido para o ROG.e, e discuti com a Moyra alguns pontos que poderiam ser melhorados em relação as simulações - há parametros que mesmo para cálculos pequenos deveriam ser melhor otimizados, assim como haviam interpretações dos dados que me pareciam conceitualmente fragéis e eu não entendi muito bem de onde ele tirou os descritores usados. Para avançarmos em relação aos cálculos que o Maicon havia começado para NVPs preciso dar uma olhada nos outputs dele, até mesmo para entender a performance das nossas máquinas. Também estou revisando a literatura de simulações desses sistemas, e espero nas próximas semanas ter uma visão mais clara do que podemos fazer com eles.

        Nas próximas duas semanas pretendo:

            - Continuar a revisão para métodos e dedicar mais tempo aos NVPs
            - Primeiras simulações: fazer testes no LAMPS, realizar calculos ONIOM no Gaussian para ver se QM/MM é uma possibilidade.
            - Aprender como obter propriedades de ligação como elf, cargas de bader, crystal orbital Hamiltonian population (ICOHP) nos cálculos ab initio.
            - Continuar com os cálculos de adsorção, passar para os casos com Li2C2O4 and Li2CO3.
            - Reproduzir o artigo de eletrolitos em campo externo
            - Discutir mais sobre os cálculos ab initio com a Camila

- [x] Ler artigo sobre NVPs

Lembrei que posso encontrar estruturas cristalograficas tanto no materials project quanto no crystallography open database e no ccdc.

O que a moyra está pensando em fazer com os NVPs está dentro do que se chama High Entropy Dopping, separei alguns artigos sobre


-----
Terça, 29 de setembro de 2026

Reunião do projeto as 14h

- [x] Rodar cálculos CO2@Li100 no QE

    Montei a estrutura 335 com duas opções de ancoragem. Os cálculos estão rodando no Desktop.

- [x] Ler artigo sobre NVPs

    Fiz um resumo sobre o artigo. O próximo passo vai ser revisar os artigos que deixei marcado e começar a procurar como se faz os cálculos de propriedades usando QE.

    Encontrei a estrutura que o Maicon havia usado nos cálculos, e mais outras duas na IUCr - a que ele usou não foi observada experimentalmente.

- [x] Revisar questões da discussão com o Victor

    falar sobre questao do monitor com o gabriel


-----
Quarta, 30 de setembro de 2026

Visita aos experimentos.

- [ ] quantum espresso:

    - [ ] como obtenho a ELF, cargas de Bader, propriedades de transporte
    
    - [ ] ver como faco NEB

    - [ ] como faco aimd

- [ ] Agora que vou rodar cálculos no Desktop, pensar em como organizar workflow (github, etc)

Vou deixar todos os arquivos no OneDrive da PUC, isso vai me permitir fazer o pós processamento na minha própria máquina.

-----

Quinta, 1 de outubro de 2026

Reunião de levantamento de recursos.

- [ ] Trabalhar na review do artigo da Moyra

    dar uma olhada no texto dela e começar a fazer revisões (se necessário)

- [ ] Trabalhar no orca e Gaussian:

    - [ ] continuar trabalhando nos calculos aimd e qmmm do li

-----
Sexta, 2 de outubro de 2026

- [ ] Wrap up

-----

# To-do list



## Simulações

gromacs:

    - [ ] revisar os resultados para licl e terminar analise

    - [ ] Realizar primeiros calculos de propriedades de transporte

    - [ ] Ler capítulo sobre modelagem de baterias aquosas e finite field modeling

    - [ ] Ler sobre campos de força em MD

quantum espresso:

    - [ ] como obtenho a ELF, cargas de Bader, propriedades de transporte
    
    - [ ] ver como faco NEB

    - [ ] como faco aimd

lammps:

    - [ ] fazer os calculos para Li+, LiCl @ H2O

    - [ ] verificar como vou poder fazer os calculos para eletrodos 

- [ ] Aprender cálculo de Barreira de Difusão Iônica usando NEB (Nudged Elastic Band)

    Sugestões do Gemini para tutoriais incluem aqueles do QE, ASE, Materials Square Blog. Ele também recomenda o artigo: Factors that affect Li mobility in layered lithium transition metal oxides. Já está salvo no zotero.

-----
