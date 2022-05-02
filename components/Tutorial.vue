<template>

    <div>
        

<!--centralizar o conteúdo da página -->

<div class="flex justify-center items-center h-screen w-full bg-white" >

    <!-- inserir card conforme documentação https://flowbite.com/docs/components/card/  padding no eixo x : "px" -->

<div class=" px-60 py-10 max-w-sm bg-white rounded-lg border border-gray-200 shadow-md dark:bg-gray-800 dark:border-gray-700">
    
    <!-- centraliza -->

    <div class="flex flex-wrap justify-center">
  
   <div class="w-6/12 sm:w-4/12">

  <!-- inserir logotipo e alinhar ao centro -->
  
<!-- imagem tava aqui antes kk--> 
  <br><br> 
</div>
    <div class="flex justify-center">
      <div class="mb-3 xl:w-96">
  
    <div class="flex justify-center"> 
      <br> 
 <div class="flex flex-col items-center pb-10">
     <img class="mb-3 w-24 h-24 content-center" src="https://hdx-prd-web-cms-upload-bucket.s3.amazonaws.com/logo_hashdex_blue_logo_d7a79b7ff9.svg"  alt="logo hashdex">     
    
    <div style="text-align:justify">
      <label for="exampleFormControlInput1" class="form-label inline-block mb-2 text-gray-700 m-auto md:px-1 py-1 content-center"  >Bem vindo à aplicação de consulta dos valores de criptomoedas. Os valores são gerados a partir do site CoinApi, e atualizados a cada 30 segundos quando adicionados à lista. </label>

    </div> </div>

     </div> 
       <input
        onkeyup="autocompletar()"
        type="text" name="ativo" id="ativo" placeholder="Digite o nome do ativo"
         class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white" required>
          <ul id="list" class="bg-white rounded-lg border border-gray-200 w-96 text-gray-900">

          </ul>
          
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


    <br>
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
        "https://rest.coinapi.io/v1/exchangerate/"+codigo+"/USD?apikey=FD3DD3DE-FD89-4F89-B5CB-49F798890CEC"
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
            var url="https://rest.coinapi.io/v1/exchangerate/"+codigo+"/USD?apikey=FD3DD3DE-FD89-4F89-B5CB-49F798890CEC";
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