1) Iniciar o projeto Laravel
	composer create-project --prefer-dist laravel/laravel=8.5.9 <nome_do_projeto>

2) Instalar o pacote UI
	composer require laravel/ui:^3.2.1

3) Gerar o esquelo do projeto com Vue.JS e autencicação web nativa (scaffold / esqueleto)
	php artisan ui vue --auth

4) Baixas as dependencias de front end
	npm install

5)Produzindo o bundle de front end
	npm run dev / npm run watch


Se você optar por fazer o download do projeto disponibilizado pode ser que, ao executar o comando "npm run watch", você encontre o seguinte erro "mix watch não é reconhecido como um comando interno..."

Se você encontrar este problema, fique tranquilo, a solução é simples. Execute o seguinte passo a passo:



Exclua a pasta node_modules contida dentro do projeto.

Exclua o arquivo package-lock.json (apenas por garantia).

Instale os módulos da aplicação novamente com o comando npm install. Basta executar esse comando via linha de comando estando dentro do diretório raiz do projeto.

Após a instalação dos módulos, execute o comando npm run watch novamente e veja se tudo está funcionando corretamente.
