<html>

<canvas id="gameCanvas" width="800" height="600"></canvas>

<script>
	var canvas;
	var canvasContext;
	var ballX = 50;
	var ballY = 50;
	var ballSpeedX = 10;
	var ballSpeedY = 4;
	var ballColorLevel = 0;
	var ballColor = '#FF0000';


	var player1Score = 0;
	var player2Score = 0;
	const WINNING_SCORE = 21;

	var showingWinScreen = false;

	var paddle1Y = 250;
	var paddle2Y = 250;
	const PADDLE_THICKNESS = 10;
	const PADDLE_HEIGHT = 100;

	function calculateMousePos(evt) {
		var rect = canvas.getBoundingClientRect();
		var root = document.documentElement;
		var mouseX = evt.clientX - rect.left - root.scrollLeft;
		var mouseY = evt.clientY - rect.top - root.scrollTop;
		return {
			x: mouseX,
			y: mouseY
		};
	}

	function handleMouseClick(evt) {
		if (showingWinScreen) {
			player1Score = 0;
			player2Score = 0;
			showingWinScreen = false;
		}
	}

	window.onload = function () {
		canvas = document.getElementById('gameCanvas');
		canvasContext = canvas.getContext('2d');

		var framesPerSecond = 30;
		setInterval(function () {
			moveEverything();
			drawEverything();
		}, 1000 / framesPerSecond);

		canvas.addEventListener('mousedown', handleMouseClick);

		canvas.addEventListener('mousemove',
			function (evt) {
				var mousePos = calculateMousePos(evt);
				paddle1Y = mousePos.y - (PADDLE_HEIGHT / 2);
			});
	}

	function ballReset() {
		if (player1Score >= WINNING_SCORE ||
			player2Score >= WINNING_SCORE) {

			showingWinScreen = true;

		}

		ballSpeedX = -ballSpeedX;
		ballX = canvas.width / 2;
		ballY = canvas.height / 2;
		paddle2Y = canvas.height / 2;

		ballColorLevel = 0;
		ballColor = '#FF0000';
	}

	function computerMovement() {
		var paddle2YCenter = paddle2Y + (PADDLE_HEIGHT / 2);
		if (paddle2YCenter < ballY - 10) {
			paddle2Y = paddle2Y + 5;
		} else if (paddle2YCenter > ballY + 10) {
			paddle2Y = paddle2Y - 5;
		}
	}

	function moveEverything() {
		if (showingWinScreen) {
			return;
		}

		computerMovement();

		ballX = ballX + ballSpeedX;
		ballY = ballY + ballSpeedY;

		if (ballX < 0) {
			if (ballY > paddle1Y &&
				ballY < paddle1Y + PADDLE_HEIGHT) {
				ballSpeedX = -ballSpeedX;

				var deltaY = ballY
					- (paddle1Y + PADDLE_HEIGHT / 2);
				ballSpeedY = deltaY * 0.35;
			} else {
				player2Score++;
				ballReset();
			}
		}
		if (ballX > canvas.width) {
			if (ballY > paddle2Y &&
				ballY < paddle2Y + PADDLE_HEIGHT) {
				ballSpeedX = -ballSpeedX;

				var deltaY = ballY
					- (paddle2Y + PADDLE_HEIGHT / 2);
				ballSpeedY = deltaY * 0.35;
			} else {
				player1Score++;
				ballReset();
			}
		}
		if (ballY < 0) {
			ballSpeedY = -ballSpeedY;
		}
		if (ballY > canvas.height) {
			ballSpeedY = -ballSpeedY;
		}
		colorBallTime();
	}

	function drawNet() {
		for (var i = 0; i < canvas.height; i += 40) {
			colorRect(canvas.width / 2 - 1, i, 2, 20, 'white');
		}
	}

	function drawEverything() {
		colorRect(0, 0, canvas.width, canvas.height, '#000000');

		if (showingWinScreen) {
			canvasContext.fillStyle = '#ffff73';

			if (player1Score >= WINNING_SCORE) {
				canvasContext.fillText("PEOPLE WON", 350, 200);
				canvasContext.fillText("PINGPONGBALL", 150, canvas.height / 2 - 200);

				canvasContext.fillText("PEOPLE VS COUMPUTER", 130, canvas.height / 2 - 190);
			} else if (player2Score >= WINNING_SCORE) {
				canvasContext.fillText("COUMPUTER WON", 350, 200);
				canvasContext.fillText("PINGPONGBALL", 150, canvas.height / 2 - 200);

				canvasContext.fillText("PEOPLE VS COUMPUTER", 130, canvas.height / 2 - 190);
			}

			canvasContext.fillText("click to continue", 350, 500);
			return;
		}

		drawNet();

		// this is left player paddle
		colorRect(0, paddle1Y, PADDLE_THICKNESS, PADDLE_HEIGHT, '#0000ff');

		// this is right computer paddle
		colorRect(canvas.width - PADDLE_THICKNESS, paddle2Y, PADDLE_THICKNESS, PADDLE_HEIGHT, '#E74C3C');

		// next line draws the ball
		colorCircle(ballX, ballY, 10, ballColor);

		canvasContext.fillStyle = '#ffff73';
		canvasContext.fillText("Score " + player1Score, 100, 300);
		canvasContext.fillText("Score " + player2Score, canvas.width - 100, 300);
		canvasContext.fillText("PINGPONGBALL", 150, canvas.height / 2 - 200);

		canvasContext.fillText("Human VS COUMPUTER", 130, canvas.height / 2 - 190);
	}

	function colorCircle(centerX, centerY, radius, drawColor) {
		canvasContext.fillStyle = drawColor;
		canvasContext.beginPath();
		canvasContext.arc(centerX, centerY, radius, 0, Math.PI * 2, true);
		canvasContext.fill();
	}

	function colorRect(leftX, topY, width, height, drawColor) {
		canvasContext.fillStyle = drawColor;
		canvasContext.fillRect(leftX, topY, width, height);
	}

	function colorBallTime() {

		switch (ballColorLevel) {
			case 00: ballColor = '#ff0000';
				break;
			case 05: ballColor = '#ff7000';
				break;
			case 10: ballColor = '#ff7300';
				break;
			case 15: ballColor = '#ff7700';
				break;
			case 20: ballColor = '#ff7f00';
				break;
			case 25: ballColor = '#ff7ff0';
				break;
			case 40: ballColor = '#ffff00';
				break;
			case 60: ballColor = '#00ff00';
				break;
			case 80: ballColor = '#0000ff';
				break;
			case 100: ballColor = '#9400d3';
				ballColorLevel = -20;
				break;

			default:
				break;
		}

		ballColorLevel += 1;
	}
//Hope no one can see 3301/Three. Three. Can you hear me? There is a hole shining in the holes between your eyelids. I have never been to Versailles before. I want to be loved. Zero. I am standing behind you now. One. I am two of us, standing behind you now. The goddess eats the city in the sea. Is. There's a hole in the floor with an answer waiting in it. God. Look, you're hatching. You're hatching!
</script>

</html>

