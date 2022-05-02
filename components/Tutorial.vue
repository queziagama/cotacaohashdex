<template>

<!-- 
  Justificativas e tentativas do projeto

  - Proposta do projeto:

  - Desenvolver uma página utilizando NuxtJS e TailwindCSS para visualizar cotação de criptoativos. 
A página deve utilizar dólar como par de referência (ex: Btc/USD), mostrando a cotação do BTC inicialmente
e incluindo um formulário (input text) que seleciona o criptoativo conforme requisição do usuário. 
Todas as informações devem ser amarzenadas numa lista que é atualizada a cada 30s, conforme api da CoinApi.
  
  Problemas enfrentados:

  - documentação e tutoriais desatualizados, tanto do nuxt como sobre a configuração da api;
  - problemas na instalação de bibliotecas, como Algolia, que facilitariam o trabalho com a busca integrada à API (função removida nas últimas atualizações do projeto)
  - informações desencontradas, como a instalação do Tailwind por exemplo, que não gerou os arquivos base CSS
  - não consegui incorporar as fontes por problema na instalação do tailwind, e do google fonts que corrompia o arquivo;

  Soluções encontradas:

  - desenvolver com javascript (já que não consegui configurar as bibliotecas); 
  - incorporar as características do Tailwind a um único template no arquivo Vue, também pelo problema de instalação e reconhecimento das bibliotecas.
  
  Recursos: 
  - documentações (vue, nuxt, tailwind, javascript);
  - ajuda de outros desenvolvedores que conhecem javascript;
  - blogs e outros projetos feitos no github, codepen e sandbox (grande maioria corrompidos por conta das atualizações do nuxt);
  - youtube;
  - stackoverflow;

-->
    <div>

<!--centralizar o conteúdo da página -->

<div class="flex justify-center items-center h-screen w-full bg-white" >

    <!-- inserir card conforme documentação https://flowbite.com/docs/components/card/  padding no eixo x : "px" -->

<div class=" px-60 py-10 max-w-sm bg-white rounded-lg border border-gray-200 shadow-md dark:bg-gray-800 dark:border-gray-700">
    
    <!-- centraliza -->

    <div class="flex flex-wrap justify-center">
  
   <div class="w-6/12 sm:w-4/12">

  <br><br> 
</div>
    <div class="flex justify-center">
      <div class="mb-3 xl:w-96">
  
    <div class="flex justify-center"> 
      <br> 
      <!-- centralizar imagem -->

 <div class="flex flex-col items-center pb-10">
     <img class="mb-3 w-24 h-24 content-center" src="https://hdx-prd-web-cms-upload-bucket.s3.amazonaws.com/logo_hashdex_blue_logo_d7a79b7ff9.svg"  alt="logo hashdex">     
    
    <div style="text-align:justify"> <!-- alinhar o texto -->

      <label for="exampleFormControlInput1" class="form-label inline-block mb-2 text-gray-700 m-auto md:px-1 py-1 content-center"  >Bem vindo à aplicação de consulta dos valores de criptomoedas. Os valores são gerados a partir do site CoinApi, e atualizados a cada 30 segundos quando adicionados à lista. </label>

    </div> </div>

<!-- input que liga ao script para adicionar a moeda à lista, "class" é reponsável pela configuração da caixa -->
     </div> 
       <input
        onkeyup="autocompletar()"
        type="text" name="ativo" id="ativo" placeholder="Digite o nome do ativo"
         class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
          <ul id="list" class="bg-white rounded-lg border border-gray-200 w-96 text-gray-900">

          </ul>

        <!-- no script foram usados a função async e fetch para autocompletar com base no Json retornado pela api,
         e as definições de variáveis (lista, ativos, símbolo, valor do token, atualizar a cada 30s), e métodos (como o document.getElementById) para execução
          -->  
    <script>

    async function autocompletar(){ 
    const list = document.getElementById('list');
        var entrada=document.getElementById('ativo').value.trim().toLowerCase();
        if(entrada.length<1){
            list.innerHTML='';
        }
        const response = await fetch(
            "ativos.json"
        );
        const ativos = await response.json();

        ativos.forEach((ativo) => {
              if(entrada == ativo.nome.toLowerCase() || entrada == ativo.codigo.toLowerCase()){
                const li = document.createElement('li');
                li.className='px-6 py-2 border-b border-gray-200 w-full';
                li.innerHTML= `<a href="javascript:adicionarALista('${ativo.nome}','${ativo.codigo}')">${ativo.nome} (${ativo.codigo})</a>`;
                list.appendChild(li);
              }
            });
    }

    </script>
      </div>
    </div>


    <br> <!-- Configuração do botão de adicionar à lista, linkando com função async "adicionaraLista" -->
    <div class="flex space-x-2 justify-center">
    <button onclick="adicionarALista()" type="button" class=" px-6 py-5 bg-gradient-to-r from-blue-300 to-blue-400 hover:from-blue-400 text-white font-medium text-xxs leading-tight rounded-full shadow-md hover:bg-blue-500 hover:shadow-lg focus:bg-blue-500 focus:shadow-lg focus:outline-none focus:ring-0 active:bg-blue-600 active:shadow-lg transition duration-150 ease-in-out">Adicionar</button>
    </div>
<br><br>
</div>
<br><br>
    <div class="flex justify-center" space-y-4 >
        <br><br>
    <div class="flex justify-center">
    <ul id="listaDeAtivos" class="bg-white rounded-lg border border-gray-200 w-96 text-gray-900">
    </ul>
    </div> </div>

<!-- abaixo a função adicionarALista, que pega os elementos pelo id (gerado pelo arquivo Json da coinApi), filtra
e adiciona à lista conforme requisição do usuário, e as mantém gravadas na lista

depois, a função para atualizar as cotações automaticamente a cada 30s. como a api é limitada a 100 requisições por dia,
pode ser que o arquivo se corrompa, gerando o valor $undefined  -->

    <script>
    async function adicionarALista(nome,codigo){
        const list = document.getElementById('list');
        list.innerHTML='';
        if(!codigo){
            var codigo=document.getElementById("ativo").value;

            const response = await fetch(
                "ativos.json"
            );
            const ativos = await response.json();

            ativos.forEach((ativo) => {
              if(codigo.toLowerCase() == ativo.nome.toLowerCase() || codigo.toLowerCase() == ativo.codigo.toLowerCase()){
                codigo=ativo.codigo;
                nome=ativo.nome;
              }
            });

        }
        document.getElementById("ativo").value='';
        const response = await fetch(
        //codigo+".json"
        "https://rest.coinapi.io/v1/exchangerate/"+codigo+"/USD?apikey=980A3C41-23B2-4E4A-9AE6-9493D55C58BB"
        );
        const json = await response.json();
        var html=document.getElementById('listaDeAtivos').innerHTML;
        var valor='<li class="px-6 py-2 border-b border-gray-200 w-full">'+nome+' ('+codigo+') $';
        valor=valor+'<span x-code="'+codigo+'">'+json.rate+'</span></li>';
        document.getElementById('listaDeAtivos').innerHTML=html+valor
    }
    adicionarALista('Bitcoin','BTC');


    async function atualizarCotacoes() {
        var xCodes=document.querySelectorAll("span[x-code]");
        xCodes.forEach( async (code)=>{
            var codigo=code.getAttribute("x-code");
            console.log(codigo);
            var url="https://rest.coinapi.io/v1/exchangerate/"+codigo+"/USD?apikey=980A3C41-23B2-4E4A-9AE6-9493D55C58BB";
            let response = await fetch(url);
            let json = await response.json();
            if (response.ok) {
                code.innerHTML=json.rate+' 🔄';
            }
        });
    }
    var atualizarACada=30;//segundos
    setInterval(atualizarCotacoes, atualizarACada*1000);
    </script>

    </div> </div> </div> 
</template>
