<template>
    <div>
        <div id="counter">0</div>
        <div id="counter_lose">0</div>

        <div id="question">
            <span id="play_question" class="glyphicon glyphicon-play" aria-hidden="true"></span>
        </div>
        <div id="answers">
            <div class="row">
                <div class="col-md-6 answer"></div>
                <div class="col-md-6 answer"></div>
            </div>
            <div class="row">
                <div class="col-md-6 answer"></div>
                <div class="col-md-6 answer"></div>
            </div>
        </div>
    </div>
</template>

<script>
    process.env['ELECTRON_DISABLE_SECURITY_WARNINGS'] = 'true';

    // electron: process.versions.electron,
    // node: process.versions.node,
    // platform: require('os').platform(),
    // vue: require('vue/package.json').version

    window.jQuery = window.$ = require('jquery');
    window.Bootstrap = require('bootstrap');

        function getRandomLetter() {
            let letters = 'абвгдеёжзийклмнопрстуфхцчшщъыьэюя';
            return function () {
                let randomLetter = letters.charAt(Math.floor(Math.random() * letters.length));
                letters = letters.replace(randomLetter, '');
                return randomLetter;
            }
        }
        function getRandomSyllable() {
            let consonants = 'бвгджзклмнпрстфхцчшщ'; // 20
            let vowels = 'аеёиоуыэюя'; // 10
            return function () {
                let randomConsonant = consonants.charAt(Math.floor(Math.random() * consonants.length));
                consonants = consonants.replace(randomConsonant, '');
                let randomVovel = vowels.charAt(Math.floor(Math.random() * vowels.length));
                vowels = vowels.replace(randomVovel, '');
                return randomConsonant + randomVovel;
            }
        }

        function getRandomMultiple() {
            let a = '123';
            let b = '123456789';
            return function () {
                let randA = a.charAt(Math.floor(Math.random() * a.length));
                a = a.replace(randA, '');
                let randB = b.charAt(Math.floor(Math.random() * b.length));
                b = b.replace(randB, '');

                if (Math.random() <= 0.5) {
                    return [randB, randA];
                }
                return [randA, randB];
            }
        }

        function createQuestion(type) {
            let genRand = null;
            if (type === 'letters') {
                genRand = getRandomLetter();
            }
            if (type === 'syllable') {
                genRand = getRandomSyllable();
            }
            if (type === 'multiple') {
                genRand = getRandomMultiple();
            }

            let randomVariant = Math.floor(Math.random() * (4 - 1));
            console.log(genRand);
            answer = genRand();
            audio.src = 'static/sound/' + type + '/' + answer + '.mp3';
            $('.answer').each(function (i, v) {
                let variant = (i === randomVariant) ? answer : genRand();
                $(v).text(variant);
            });
        }
        function init() {
            createQuestion('syllable');

            $('#play_question').click(function () {
                audio.play();
            });
            $('.answer').click(function () {
                if ($(this).text() === answer) {
                    let counter = parseInt($('#counter').text());
                    $('#counter').text(counter + 1);
                    if (counter + 1 === 15) {
                        let img = new Image();
                        img.src = 'static/win.png';
                        $('body').empty().append(img);
                    }
                } else {
                    $('#counter').text(0);
                    let counterLose = parseInt($('#counter_lose').text());
                    $('#counter_lose').text(counterLose + 1);
                    if (counterLose + 1 == 3) {
                        let img = new Image();
                        img.src = 'static/lose.png';
                        $('body').empty().append(img);
                    }
                }
                createQuestion();
            });
        }
        let answer = "";
        let audio = new Audio();
        window.onload = function () {
            init();
        };


    export default {
        name: 'Main',
        data() {
            return {}
        },
        methods: {
            open(link) {
                this.$electron.shell.openExternal(link)
            }
        }
    }
</script>

<style type="text/css">
    #question {
        height: 50vh;
        background-color: #e0e0e0;

        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 20em;
    }

    #counter {
        position: absolute;
        top: 0;
        right: 20px;
        font-size: 20em;
        background-color: #6495ED;
    }

    #counter_lose {
        position: absolute;
        top: 0;
        left: 20px;
        font-size: 20em;
        background-color: red;
    }

    .answer {
        height: 25vh;
        background-color: #ddd;
    }

    .answer:hover {
        background-color: #eee;
    }

    .row {
        padding: 0;
        margin: 0;
    }

    .col-md-6 {
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 10em;
    }
</style>
