<script lang="ts">
	type BibleBook = {
		code: string;
		title: string;
	};

	function removeAccents(str: string): string {
		return str.normalize('NFD').replace(/\p{Diacritic}/gu, '');
	}

	const bibleBooks = [
		'Gênesis',
		'Êxodo',
		'Levítico',
		'Números',
		'Deuteronômio',
		'Josué',
		'Juízes',
		'Rute',
		'1 Samuel',
		'2 Samuel',
		'1 Reis',
		'2 Reis',
		'1 Crônicas',
		'2 Crônicas',
		'Esdras',
		'Neemias',
		'Ester',
		'Jó',
		'Salmos',
		'Provérbios',
		'Eclesiastes',
		'Cântico de Salomão',
		'Isaías',
		'Jeremias',
		'Lamentações',
		'Ezequiel',
		'Daniel',
		'Oseias',
		'Joel',
		'Amós',
		'Obadias',
		'Jonas',
		'Miqueias',
		'Naum',
		'Habacuque',
		'Sofonias',
		'Ageu',
		'Zacarias',
		'Malaquias',
		'Mateus',
		'Marcos',
		'Lucas',
		'João',
		'Atos',
		'Romanos',
		'1 Coríntios',
		'2 Coríntios',
		'Gálatas',
		'Efésios',
		'Filipenses',
		'Colossenses',
		'1 Tessalonicenses',
		'2 Tessalonicenses',
		'1 Timóteo',
		'2 Timóteo',
		'Tito',
		'Filêmon',
		'Hebreus',
		'Tiago',
		'1 Pedro',
		'2 Pedro',
		'1 João',
		'2 João',
		'3 João',
		'Judas',
		'Apocalipse'
	];

	const bibleBooksMap = new Map<string, BibleBook[]>();

	for (const bibleBook of bibleBooks) {
		const code = removeAccents(bibleBook);
		const letter = code[0];

		bibleBooksMap.set(letter, [
			...(bibleBooksMap.get(letter) ?? []),
			{
				code,
				title: bibleBook
			}
		]);
	}

	const letters = ['1', '2', '3'];
	for (let i = 65; i <= 90; i++) {
		letters.push(String.fromCharCode(i));
	}

	let selectedLetter: string | null = null;
</script>

<h1 class="p-3 text-3xl font-bold bg-purple-600 border-b-2 border-purple-800 text-white">
	{selectedLetter ?? 'Escolha uma Letra'}
</h1>

<div class="p-2">
	<button
		class="px-5 py-1 my-2 border-2 bg-purple-800 hover:bg-purple-700 border-purple-900 text-white"
		on:click={() => (selectedLetter = null)}>Voltar</button
	>

	<div class="flex gap-2 flex-wrap max-w-4xl m-auto">
		{#if !selectedLetter}
			{#each letters as letter}
				<button
					class="w-10 h-10 border bg-purple-600 hover:bg-purple-800 border-purple-900 text-white flex items-center justify-center disabled:opacity-75 disabled:border-gray-500 disabled:bg-gray-500"
					disabled={!bibleBooksMap.has(letter)}
					on:click={() => (selectedLetter = letter)}
				>
					{letter}
				</button>
			{/each}
		{/if}

		{#if selectedLetter}
			{#each bibleBooksMap.get(selectedLetter) as bibleBook}
				<a
					href="#/{bibleBook.code}"
					class="px-5 py-1 border bg-purple-300 hover:bg-purple-400 text-purple-800 flex items-center justify-center"
				>
					{bibleBook.title}
				</a>
			{/each}
		{/if}
	</div>
</div>
