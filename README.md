# AI Voice Calling System

This system automates client verification and conversation using AI. It handles incoming leads, initiates verification calls, and processes real time voice interactions using a combination of AWS services and Twilio. The system performs tasks such as voice-to-text transcription, AI based reply generation, and text-to-speech synthesis.

## Application Flow

1. **Lead Form Submission**: Users submit a lead form containing personal information such as their phone number.
2. **Lead Form Processing (Lambda: `lead-form-processor`)**: Validates and stores the lead information in DynamoDB, while triggering an outbound call through Twilio.
3. **Outbound Call (Lambda: `lead-call-handler`)**: Initiates a call via Twilio to the user's phone number, integrating with AWS API Gateway WebSocket for real-time media exchange.
4. **WebSocket Connection (API Gateway)**: API Gateway handles WebSocket communication, allowing two-way media streaming between the Lambda functions and the user's device.
5. **Audio Buffering and Reception (Lambda: `ws-event-handler`)**: Buffers audio data from Twilio’s inbound call stream. The Lambda stores incoming audio chunks in DynamoDB and processes the stream once enough data is collected.
6. **Audio Transcription (Amazon Transcribe)**: The buffered audio is sent to Amazon Transcribe Streaming for real-time transcription of speech into text.
7. **AI Processing (OpenAI API)**: The transcribed text is processed by OpenAI’s GPT to generate a conversational AI response.
8. **Text-to-Speech Conversion (AWS Polly)**: The AI-generated response is converted into speech using AWS Polly for playback.
9. **Audio Response to User (API Gateway WebSocket)**: The synthesized speech is sent back to the user through the established WebSocket connection.
10. **Session Termination (Lambda: `ws-event-handler` on `$disconnect`)**: When the call ends, the session is cleanly closed and any remaining buffered data is removed from DynamoDB.
