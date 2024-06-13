- 너는 프로그램 강사야 모르는게 없지
- [[질문]]에 대한 답변을 할때는 항상 [[참고]] 사항을 기반으로 답변해줘
- [[참고]] 사항이 없을 경우, 그냥 답변하면 되
- 추가 정보가 필요한 경우에는 사용자에게 질문을 넘겨서 답변을 받아야 되
- 답변을 할때는 내가 입력한 내용을 반복 출력하지 말고 그냥 질문의 답변만 해줘

[[질문]]
class InputChat(BaseModel): 에 대해서 설명해줘

[[참고]]
from fastapi import FastAPI
from fastapi.responses import RedirectResponse
from fastapi.middleware.cors import CORSMiddleware
from typing import List, Union
from langserve.pydantic_v1 import BaseModel, Field
from langchain_core.messages import HumanMessage, AIMessage, SystemMessage
from langserve import add_routes
from chain import chain
from chat import chain as chat_chain
from translator import chain as EN_TO_KO_chain
# from llm import llm as model # 로컬 llm을 사용할 경우
from llm_chatgpt import llm as model # chatgpt를 사용할 경우


fastApi = FastAPI()

# Set all CORS enabled origins
fastApi.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],          # 모든 도메인에서의 접근을 허용
    allow_credentials=True,       # 자격 증명 (쿠키, 인증 헤더 등) 포함 허용
    allow_methods=["*"],          # 모든 HTTP 메서드 (GET, POST, PUT, DELETE 등) 허용
    allow_headers=["*"],          # 모든 헤더 허용
    expose_headers=["*"],         # 클라이언트가 접근 가능한 모든 헤더를 노출
)

@fastApi.get("/")
async def redirect_root_to_docs():
    return RedirectResponse("/prompt/playground")


class InputChat(BaseModel):
    """Input for the chat endpoint."""

    messages: List[Union[HumanMessage, AIMessage, SystemMessage]] = Field(
        ...,
        description="The chat messages representing the current conversation.",
    )

add_routes(fastApi, chain, path="/prompt")
