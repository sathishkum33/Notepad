<--
<div class="container">
  <div class="chatbot-container">
    <div class="chatbot-circle">
      <span class="chatbot-text">chatbot</span>
    </div>
  </div>
</div>

-->
.container {
  position: relative;
}

.chatbot-container {
  position: absolute;
  top: 20px; /* Adjust the positioning as per your requirements */
  right: 20px; /* Adjust the positioning as per your requirements */
}

.chatbot-circle {
  position: relative;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background-color: #007bff;
  animation: pulse 1s infinite;
  display: flex;
  justify-content: center;
  align-items: center;
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

.chatbot-text {
  position: absolute;
  color: #fff;
  font-size: 16px;
  text-align: center;
  transform: rotate(-45deg);
}
